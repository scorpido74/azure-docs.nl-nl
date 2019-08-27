---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051236"
---
### <a name="container-repositories-and-images"></a>Container opslagplaatsen en installatie kopieën

In de onderstaande tabellen vindt u een uitgebreid overzicht van de beschik bare container installatie kopieën die door Azure Cognitive Services worden aangeboden.

#### <a name="public-container-registry-mcrmicrosoftcom"></a>Openbaar (container register: `mcr.microsoft.com`)

De micro soft-Container Registry host alle algemene Beschik baarheid (GA)-containers voor Cognitive Services.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sentimentanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>Open bare Preview (container register `containerpreview.azurecr.io`:)

Het REGI ster van de container voorbeeld bevat alle containers voor de open bare preview-versie voor Cognitive Services die nog niet zijn uitgevoerd op algemene Beschik baarheid (GA). Voor deze containers is een formele aanvraag voor toegang vereist om ze te kunnen gebruiken.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Anomalie detectie](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Tekst herkennen | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulier herkenning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service-API](../../speech-service/speech-container-howto.md) | Spraak-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md) | Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
