---
title: Spraakcontainers installeren
titleSuffix: Azure Cognitive Services
description: Geeft details over de configuratieopties voor het aan de werk-naar-tekst helmdiagram.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971321"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Spraak-naar-tekst (subgrafiek: grafieken/speechToText)

Als u de "paraplu"-grafiek `speechToText.` wilt overschrijven, voegt u het voorvoegsel op een parameter toe om deze specifieker te maken. Het zal bijvoorbeeld de bijbehorende parameter `speechToText.numberOfConcurrentRequest` overschrijven, bijvoorbeeld overschrijft `numberOfConcurrentRequest`.

|Parameter|Beschrijving|Standaard|
| -- | -- | -- |
| `enabled` | Of de **spraak-naar-tekstservice** is ingeschakeld. | `false` |
| `numberOfConcurrentRequest` | Het aantal gelijktijdige aanvragen voor de **spraak-naar-tekstservice.** Deze grafiek berekent automatisch CPU- en geheugenbronnen op basis van deze waarde. | `2` |
| `optimizeForAudioFile`| Of de service moet optimaliseren voor audio-invoer via audiobestanden. Als `true`, zal deze grafiek meer CPU-bron toewijzen aan service. | `false` |
| `image.registry`| Het **afbeeldingsregister van de tekst-naar-tekstdocker.** | `containerpreview.azurecr.io` |
| `image.repository` | De **spraak-naar-tekst** docker image repository. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | De **afbeeldingstag van de tekst-naar-tekstdocker.** | `latest` |
| `image.pullSecrets` | De afbeelding is geheimen voor het trekken van de **spraak-naar-tekst** docker afbeelding. | |
| `image.pullByHash`| Of het dockerbeeld door hash wordt getrokken. `true`Indien, `image.hash` is vereist. | `false` |
| `image.hash`| De **spraak-naar-tekst** docker afbeelding hash. Alleen gebruikt `image.pullByHash: true`wanneer .  | |
| `image.args.eula`(verplicht) | Geeft aan dat je de licentie hebt geaccepteerd. De enige geldige waarde is`accept` | |
| `image.args.billing`(verplicht) | De URI-waarde voor factureringseindpunt is beschikbaar op de pagina Spraakoverzicht van de Azure-portal. | |
| `image.args.apikey`(verplicht) | Wordt gebruikt om factureringsgegevens bij te houden. ||
| `service.type` | Het Kubernetes-servicetype van de **spraak-naar-tekstservice.** Bekijk de [instructies voor Kubernetes-servicetypen](https://kubernetes.io/docs/concepts/services-networking/service/) voor meer informatie en controleer de ondersteuning van cloudprovideren. | `LoadBalancer` |
| `service.port`|  De poort van de **spraak-naar-tekstservice.** | `80` |
| `service.annotations` | De **spraak-naar-tekst-annotaties** voor de metagegevens van de service. Annotaties zijn sleutelwaardeparen. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Of de [Horizontale Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is ingeschakeld. Als `true`, `speech-to-text-autoscaler` wordt het geïmplementeerd in het Kubernetes-cluster. | `true` |
| `service.podDisruption.enabled` | Of het [pod-onderbrekingsbudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) is ingeschakeld. Als `true`, `speech-to-text-poddisruptionbudget` wordt het geïmplementeerd in het Kubernetes-cluster. | `true` |