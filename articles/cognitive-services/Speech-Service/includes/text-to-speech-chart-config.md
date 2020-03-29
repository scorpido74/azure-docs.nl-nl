---
title: Spraakcontainers installeren
titleSuffix: Azure Cognitive Services
description: Geeft details over de configuratieopties voor de configuratie van de configuratie van het helmdiagram voor tekst naar spraak.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971305"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Tekst-naar-spraak (subgrafiek: grafieken/textToSpeech)

Als u de "paraplu"-grafiek `textToSpeech.` wilt overschrijven, voegt u het voorvoegsel op een parameter toe om deze specifieker te maken. Het zal bijvoorbeeld de bijbehorende parameter `textToSpeech.numberOfConcurrentRequest` overschrijven, bijvoorbeeld overschrijft `numberOfConcurrentRequest`.

|Parameter|Beschrijving|Standaard|
| -- | -- | -- |
| `enabled` | Of de **tekst-naar-spraakservice** is ingeschakeld. | `false` |
| `numberOfConcurrentRequest` | Het aantal gelijktijdige aanvragen voor de **tekst-naar-spraakservice.** Deze grafiek berekent automatisch CPU- en geheugenbronnen op basis van deze waarde. | `2` |
| `optimizeForTurboMode`| Of de service moet optimaliseren voor tekstinvoer via tekstbestanden. Als `true`, zal deze grafiek meer CPU-bron toewijzen aan service. | `false` |
| `image.registry`| Het **afbeeldingsregister van de tekst-naar-spraakdocker.** | `containerpreview.azurecr.io` |
| `image.repository` | De **tekst-naar-spraak** docker image repository. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | De **afbeeldingstag van de tekst-naar-spraakdocker.** | `latest` |
| `image.pullSecrets` | De afbeelding is geheimen voor het trekken van de **tekst-naar-spraak** docker afbeelding. | |
| `image.pullByHash`| Of het dockerbeeld door hash wordt getrokken. `true`Indien, `image.hash` is vereist. | `false` |
| `image.hash`| De **tekst-naar-spraak** docker afbeelding hash. Alleen gebruikt `image.pullByHash: true`wanneer .  | |
| `image.args.eula`(verplicht) | Geeft aan dat je de licentie hebt geaccepteerd. De enige geldige waarde is`accept` | |
| `image.args.billing`(verplicht) | De URI-waarde voor factureringseindpunt is beschikbaar op de pagina Spraakoverzicht van de Azure-portal. | |
| `image.args.apikey`(verplicht) | Wordt gebruikt om factureringsgegevens bij te houden. ||
| `service.type` | Het Kubernetes-servicetype van de **tekst-naar-spraakservice.** Bekijk de [instructies voor Kubernetes-servicetypen](https://kubernetes.io/docs/concepts/services-networking/service/) voor meer informatie en controleer de ondersteuning van cloudprovideren. | `LoadBalancer` |
| `service.port`|  De poort van de **tekst-naar-spraakservice.** | `80` |
| `service.annotations` | De **tekst-naar-spraak-annotaties** voor de metagegevens van de service. Annotaties zijn sleutelwaardeparen. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Of de [Horizontale Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) is ingeschakeld. Als `true`, `text-to-speech-autoscaler` wordt het geïmplementeerd in het Kubernetes-cluster. | `true` |
| `service.podDisruption.enabled` | Of het [pod-onderbrekingsbudget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) is ingeschakeld. Als `true`, `text-to-speech-poddisruptionbudget` wordt het geïmplementeerd in het Kubernetes-cluster. | `true` |