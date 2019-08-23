---
title: Spraak containers installeren
titleSuffix: Azure Cognitive Services
description: Details van de configuratie opties voor de helm voor spraak naar tekst.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971321"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Spraak naar tekst (subdiagram: grafieken/speechToText)

Als u het schema "paraplu" wilt overschrijven, voegt `speechToText.` u het voor voegsel toe aan een para meter om het meer specifiek te maken. Hiermee wordt bijvoorbeeld de overeenkomstige para meter overschreven zoals `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest`onderdrukkingen.

|Parameter|Description|Standaard|
| -- | -- | -- |
| `enabled` | Hiermee wordt aangegeven of de **spraak-naar-tekst-** service is ingeschakeld. | `false` |
| `numberOfConcurrentRequest` | Het aantal gelijktijdige aanvragen voor de service voor **spraak naar tekst** . In deze grafiek worden automatisch de CPU-en geheugen bronnen berekend op basis van deze waarde. | `2` |
| `optimizeForAudioFile`| Of de service moet worden geoptimaliseerd voor audio-invoer via audio bestanden. Als `true`deze grafiek meer CPU-resource wordt toegewezen aan de service. | `false` |
| `image.registry`| Het REGI ster **met spraak-naar-tekst** docker-installatie kopieën. | `containerpreview.azurecr.io` |
| `image.repository` | De afbeeldings opslagplaats **voor spraak-naar-tekst-** docker. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | De tag **voor spraak-naar-tekst-** docker-afbeelding. | `latest` |
| `image.pullSecrets` | De afbeeldings geheimen voor het ophalen van de **spraak-naar-tekst** docker-installatie kopie. | |
| `image.pullByHash`| Hiermee wordt aangegeven of de docker-installatie kopie wordt opgehaald door hash. Als `true`isvereist. `image.hash` | `false` |
| `image.hash`| De hash van de **spraak-naar-tekst-** docker-installatie kopie. Wordt alleen gebruikt `image.pullByHash: true`wanneer.  | |
| `image.args.eula`lang | Hiermee wordt aangegeven dat u de licentie hebt geaccepteerd. De enige geldige waarde is`accept` | |
| `image.args.billing`lang | De waarde voor de URL van het facturerings eindpunt is beschikbaar op de overzichts pagina van het Azure Portal. | |
| `image.args.apikey`lang | Gebruikt voor het bijhouden van informatie over facturering. ||
| `service.type` | Het Service type Kubernetes van de service **voor spraak naar tekst** . Zie de [instructies voor het Kubernetes-Service type](https://kubernetes.io/docs/concepts/services-networking/service/) voor meer informatie en controleer de ondersteuning van cloud providers. | `LoadBalancer` |
| `service.port`|  De poort van de service voor **spraak naar tekst** . | `80` |
| `service.annotations` | De aantekeningen voor **spraak naar tekst** voor de meta gegevens van de service. Annotaties zijn sleutel waardeparen. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Hiermee wordt aangegeven of de horizontale pod-functie voor [automatisch schalen](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is ingeschakeld. Als `true`, wordt `speech-to-text-autoscaler` de geïmplementeerd in het Kubernetes-cluster. | `true` |
| `service.podDisruption.enabled` | Of het [pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) -verstorings budget is ingeschakeld. Als `true`, wordt `speech-to-text-poddisruptionbudget` de geïmplementeerd in het Kubernetes-cluster. | `true` |