---
title: Spraakcontainers configureren
titleSuffix: Azure Cognitive Services
description: Spraakservice biedt elke container een gemeenschappelijk configuratiekader, zodat u eenvoudig opslag- en logboekregistratie- en telemetrie- en beveiligingsinstellingen voor uw containers configureren en beheren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c74aa48b18661236eb55278d1e5a05215b2432c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877572"
---
# <a name="configure-speech-service-containers"></a>Container voor spraakservice configureren

Spraakcontainers stellen klanten in staat om één spraaktoepassingsarchitectuur te bouwen die is geoptimaliseerd om te profiteren van zowel robuuste cloudmogelijkheden als randplaats. De vier spraakcontainers die we nu ondersteunen zijn: **spraak-naar-tekst,** **custom-speech-to-text,** **text-to-speech**en **custom-text-to-speech**.

De runtime-omgeving **voor spraakcontainer** is geconfigureerd met de `docker run` opdrachtargumenten. Deze container heeft verschillende vereiste instellingen, samen met een paar optionele instellingen. Er zijn verschillende [voorbeelden](#example-docker-run-commands) van de opdracht beschikbaar. De containerspecifieke instellingen zijn de factureringsinstellingen.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) , en instellingen worden samen gebruikt, en u moet geldige waarden voor alle drie van hen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](speech-container-howto.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey-configuratie-instelling

De `ApiKey` instelling geeft de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. U moet een waarde opgeven voor de ApiKey en de waarde moet [`Billing`](#billing-configuration-setting) een geldige sleutel zijn voor de _spraakbron_ die is opgegeven voor de configuratie-instelling.

Deze instelling is te vinden op de volgende plaats:

- Azure-portal: **Resourcebeheer van spraak,** onder **sleutels**

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling voor factureringsconfiguratie

De `Billing` instelling geeft het eindpunt URI op van de _spraakbron_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. U moet een waarde opgeven voor deze configuratie-instelling en de waarde moet een geldig eindpunt URI zijn voor een _spraakbron_ in Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

Deze instelling is te vinden op de volgende plaats:

- Azure-portal: het overzicht **van spraak,** gelabeld`Endpoint`

| Vereist | Name | Gegevenstype | Beschrijving |
| -------- | ---- | --------- | ----------- |
| Ja | `Billing` | Tekenreeks | Factureringseindpunt URI. Zie het verzamelen van [vereiste parameters](speech-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de factureringuri. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](../cognitive-services-custom-subdomains.md)meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Eula-instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Vloeiende instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyreferenties instellingen

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Montage-instellingen

Gebruik bindingsbevestigingen om gegevens van en naar de container te lezen en te schrijven. U een invoermount of uitvoerbevestiging `--mount` opgeven door de optie op te geven in de opdracht [Docker run.](https://docs.docker.com/engine/reference/commandline/run/)

De standaardspraakcontainers gebruiken geen invoer- of uitvoerbevestigingen om trainings- of servicegegevens op te slaan. Aangepaste spraakcontainers zijn echter afhankelijk van volumemounts.

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de locatie van de [hostcomputer](speech-container-howto.md#the-host-computer)mogelijk niet toegankelijk vanwege een conflict tussen machtigingen die worden gebruikt door het dockerserviceaccount en de locatiemachtigingen voor de hostmount.

| Optioneel | Name | Gegevenstype | Beschrijving |
| -------- | ---- | --------- | ----------- |
| Niet toegestaan | `Input` | Tekenreeks | Standaard spraakcontainers maken hier geen gebruik van. Aangepaste spraakcontainers maken gebruik [van volumemounts.](#volume-mount-settings)                                                                                    |
| Optioneel | `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`. Dit is de locatie van de logs. Dit geldt ook voor containerlogboeken. <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Instellingen voor volumemontage

De aangepaste spraakcontainers gebruiken [volumemounts](https://docs.docker.com/storage/volumes/) om aangepaste modellen voort te duren. U een volumemount `-v` opgeven `--volume`door de (of) optie toe te voegen aan de opdracht [Docker run.](https://docs.docker.com/engine/reference/commandline/run/)

Aangepaste modellen worden gedownload de eerste keer dat een nieuw model wordt ingenomen als onderdeel van de aangepaste spraakcontainerdocker run opdracht. Sequentiële uitvoeringen `ModelId` van hetzelfde voor een aangepaste spraakcontainer gebruiken het eerder gedownloade model. Als de volumehouder niet aanwezig is, kunnen aangepaste modellen niet blijven bestaan.

De instelling volumebevestiging bestaat `:` uit drie kleurgescheiden velden:

1. Het eerste veld is de naam van het volume op de hostmachine, bijvoorbeeld _C:\input_.
2. Het tweede veld is de map in de container, bijvoorbeeld _/usr/local/models_.
3. Het derde veld (optioneel) is een door komma's gescheiden lijst met opties, zie voor meer informatie [gebruiksvolumes](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Voorbeeld van volumebevestiging

```bash
-v C:\input:/usr/local/models
```

Met deze opdracht wordt de hostmachine _C:\input_ directory gemonteerd op de map containers _/usr/local/models._

> [!IMPORTANT]
> De instellingen voor volumebevestiging zijn alleen van toepassing op **aangepaste spraak-naar-tekst-** en **aangepaste tekst-naar-spraakcontainers.** De standaard **spraak-naar-tekst-** **en tekst-naar-spraakcontainers** maken geen gebruik van volumemounts.

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken. Eenmaal uitgevoerd, de container blijft draaien totdat u [het stopt.](speech-container-howto.md#stop-the-container)

- **Line-continuation teken**: De Docker commando's in `\`de volgende secties gebruiken de back slash, als een lijn voortzetting teken. Vervang of verwijder dit op basis van de vereisten van uw hostbesturingssysteem.
- **Argumentvolgorde:** Wijzig de volgorde van de argumenten niet, tenzij u bekend bent met Docker-containers.

Vervang {_argument_name_} door uw eigen waarden:

| Tijdelijke aanduiding | Waarde | Opmaak of voorbeeld |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | De eindpuntsleutel van `Speech` de bron `Speech` op de pagina Azure Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | De waarde van het factureringseindpunt is beschikbaar op de pagina Azure-overzicht. `Speech` | Zie [het verzamelen van vereiste parameters](speech-container-howto.md#gathering-required-parameters) voor expliciete voorbeelden. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en de opties moeten worden opgegeven om de container uit te voeren; Anders start de container niet. Zie [Facturering voor](#billing-configuration-setting)meer informatie.
> De ApiKey-waarde is de **sleutel** van de pagina Azure Speech Resource-sleutels.

## <a name="speech-container-docker-examples"></a>Voorbeelden van spraakcontainer Docker

De volgende Voorbeelden van Docker zijn voor de container Van de Toespraak.

## <a name="speech-to-text"></a>[Spraak naar tekst](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Basisvoorbeeld voor Spraak-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Voorbeeld van logboekregistratie voor Spraak-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Aangepaste spraak-naar-tekst](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Basisvoorbeeld voor Aangepaste spraak-naar-tekst

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Voorbeeld van logboekregistratie voor Aangepaste spraak-naar-tekst

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

## <a name="text-to-speech"></a>[Tekst naar spraak](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Basisvoorbeeld voor Tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Voorbeeld van logboekregistratie voor Tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Aangepaste tekst-naar-spraak](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Basisvoorbeeld voor Aangepaste tekst-naar-spraak

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Voorbeeld van logboekregistratie voor Aangepaste tekst-naar-spraak

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

- Controleren [hoe u containers installeert en uitvoert](speech-container-howto.md)
