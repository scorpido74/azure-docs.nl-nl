---
title: Een container configureren voor Formulierherkenning
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van de container Formulierherkenning om formulier- en tabelgegevens te ontken.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bc48c0ba23e73adec312adfeeb1fcd57dba6ceec
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879153"
---
# <a name="configure-form-recognizer-containers"></a>Form Recognizer-containers configureren

Met Azure Form Recognizer-containers u een toepassingsarchitectuur bouwen die is geoptimaliseerd om te profiteren van zowel robuuste cloudmogelijkheden als randplaats.

U configureert de runtime-time-omgeving `docker run` van form recognizer met behulp van de opdrachtargumenten. Deze container heeft verschillende vereiste instellingen en een paar optionele instellingen. Zie de sectie ['Voorbeeld docker run'-opdrachten](#example-docker-run-commands) voor een paar voorbeelden. De containerspecifieke instellingen zijn de factureringsinstellingen.

> [!IMPORTANT]
> De form recognizer-containers gebruiken momenteel versie 1.0 van de API voor formulierherkenning. U hebt toegang tot de nieuwste versie van de API door in plaats daarvan de beheerde service te gebruiken.

## <a name="configuration-settings"></a>Configuratie-instellingen

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> De [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)instellingen [`Eula`](#eula-setting) en instellingen worden samen gebruikt. U moet geldige waarden opgeven voor alle drie de instellingen; anders start uw container niet. Zie Facturering voor meer informatie over het gebruik van deze configuratie-instellingen om een container te [instantiëren.](form-recognizer-container-howto.md#billing)

## <a name="apikey-configuration-setting"></a>ApiKey-configuratie-instelling

De `ApiKey` instelling geeft de Azure-bronsleutel op die wordt gebruikt om factureringsgegevens voor de container bij te houden. De waarde voor de ApiKey moet een geldige sleutel zijn voor `Billing` de _bron Formulierherkenning_ waarvoor is opgegeven in de sectie 'Factureringsconfiguratieinstelling'.

U deze instelling vinden in de Azure-portal in **Form Recognizer Resource Management**onder **Sleutels**.

## <a name="applicationinsights-setting"></a>Instelling ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Instelling voor factureringsconfiguratie

De `Billing` instelling geeft het eindpunt URI op van de _bron Formulierherkenning_ op Azure die wordt gebruikt om factureringsgegevens voor de container te meten. De waarde voor deze configuratie-instelling moet een geldig eindpunt URI zijn voor een _Form Recognizer-bron_ op Azure. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten.

U vindt deze instelling in de Azure-portal in **formulierherkenningsoverzicht**onder **Eindpunt**.

|Vereist| Name | Gegevenstype | Beschrijving |
|--|------|-----------|-------------|
|Ja| `Billing` | Tekenreeks | Factureringseindpunt URI. Zie het verzamelen van [vereiste parameters](form-recognizer-container-howto.md#gathering-required-parameters)voor meer informatie over het verkrijgen van de factureringuri. Zie [Aangepaste subdomeinnamen voor cognitieve services voor](../cognitive-services-custom-subdomains.md)meer informatie en een volledige lijst met regionale eindpunten. |

## <a name="eula-setting"></a>Eula-instelling

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Vloeiende instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>HTTP-proxyreferenties instellingen

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Instellingen voor logboekregistratie

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Montage-instellingen

Gebruik bindingsbevestigingen om gegevens van en naar de container te lezen en te schrijven. U een invoerhouder of een uitvoerbevestiging opgeven door de `--mount` optie in de [ `docker run` opdracht](https://docs.docker.com/engine/reference/commandline/run/)op te geven.

De Form Recognizer-container vereist een invoerbevestiging en een uitvoerbevestiging. De invoerbevestiging kan alleen-lezen zijn en is vereist voor toegang tot de gegevens die worden gebruikt voor training en scoren. De uitvoerbevestiging moet beschrijfbaar zijn en u gebruikt deze om de modellen en tijdelijke gegevens op te slaan.

De exacte syntaxis van de hostmountlocatie is afhankelijk van het hostbesturingssysteem. Bovendien is de berglocatie van de [hostcomputer](form-recognizer-container-howto.md#the-host-computer) mogelijk niet toegankelijk vanwege een conflict tussen de machtigingen van de Docker-serviceaccount en de locatiemachtigingen voor de hostmount.

|Optioneel| Name | Gegevenstype | Beschrijving |
|-------|------|-----------|-------------|
|Vereist| `Input` | Tekenreeks | Het doel van de invoerberg. De standaardwaarde is `/input`.    <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\input,target=/input`|
|Vereist| `Output` | Tekenreeks | Het doel van de uitgangsberg. De standaardwaarde is `/output`.  <br><br>Voorbeeld:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Voorbeeld van opdrachten voor docker-uitgevoerd

In de volgende voorbeelden worden de configuratie-instellingen `docker run` gebruikt om te illustreren hoe u opdrachten schrijven en gebruiken. Wanneer deze wordt uitgevoerd, blijft de container draaien totdat u [deze stopt.](form-recognizer-container-howto.md#stop-the-container)

* **Line-continuation teken**: De Docker-opdrachten in de\\volgende secties gebruiken een back slash ( ) als een lijn voortzetting teken. Vervang of verwijder dit teken, afhankelijk van de vereisten van uw hostbesturingssysteem.
* **Argumentvolgorde:** wijzig de volgorde van de argumenten niet, tenzij u bekend bent met Docker-containers.

Vervang {_argument_name}_ in de volgende tabel door uw eigen waarden:

| Tijdelijke aanduiding | Waarde |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | De sleutel die wordt gebruikt om de container te starten. Het is beschikbaar op de pagina Formulierherkenningssleutels van azure portal Form. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | De URI-waarde voor factureringseindpunt is beschikbaar op de pagina Overzicht van formulierherkenning voor Azure-portalformulier.|
| **{COMPUTER_VISION_API_KEY}** | De sleutel is beschikbaar op de pagina Azure portal Computer Vision API Keys.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Het eindpunt van facturering. Als u een cloudgebaseerde Computer Vision-bron gebruikt, is de URI-waarde beschikbaar op de pagina Van Azure portal Computer Vision API-overzicht. Als u een *container voor cognitief services-herkennen-tekst* gebruikt, gebruikt u de URL `docker run` van het factureringseindpunt die is doorgegeven aan de container in de opdracht. |

Zie [het verzamelen van vereiste parameters](form-recognizer-container-howto.md#gathering-required-parameters) voor meer informatie over het verkrijgen van deze waarden.

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Als u de container `Eula` `Billing`wilt `ApiKey` uitvoeren, geeft u de opties en opties op; Anders start de container niet. Zie [Facturering voor](#billing-configuration-setting)meer informatie.

## <a name="form-recognizer-container-docker-examples"></a>Voorbeelden van Form Recognizer container Docker

De volgende Voorbeelden van Docker zijn voor de container Van de Vormerkenning.

### <a name="basic-example-for-form-recognizer"></a>Basisvoorbeeld voor Formulierherkenning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Voorbeeld van logboekregistratie voor Formulierherkenning

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Volgende stappen

* [Bekijk het installeren en uitvoeren van containers](form-recognizer-container-howto.md).
