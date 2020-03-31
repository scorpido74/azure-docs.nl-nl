---
title: Overzicht van Linkerd
description: Een overzicht van Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593764"
---
# <a name="linkerd"></a>Linkerd (Linkerd)

## <a name="overview"></a>Overzicht

[Linkerd][linkerd] is een gebruiksvriendelijk en lichtgewicht servicemesh.

## <a name="architecture"></a>Architectuur

Linkerd biedt een dataplane dat bestaat uit ultralichte [Linkerd][linkerd-proxy] gespecialiseerde proxy sidecars. Deze intelligente proxy's regelen al het netwerkverkeer in en uit uw meshed apps en workloads. De proxy's leggen ook metrics bloot via [Prometheus][prometheus] metrics endpoints.

Het besturingsvlak beheert de configuratie en geaggregeerde telemetrie via de volgende [componenten:][linkerd-architecture]

- **Controller** - Biedt api die de Linkerd CLI en Dashboard aanstuurt. Biedt configuratie voor proxy's.

- **Tik** - Stel realtime horloges vast op verzoeken en antwoorden.

- **Identiteit** - Biedt identiteits- en beveiligingsmogelijkheden die mTLS tussen services mogelijk maken.

- **Web** - Biedt het Linkerd-dashboard.


Het volgende architectuurdiagram laat zien hoe de verschillende componenten in het gegevensvlak en het controlevlak met elkaar in wisselwerking staan.


![Overzicht van Linkerd componenten en architectuur.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Selectiecriteria

Het is belangrijk om de volgende gebieden te begrijpen en te overwegen bij het evalueren van Linkerd voor uw workloads:

- [Ontwerpprincipes](#design-principles)
- [Functionaliteit](#capabilities)
- [Scenario 's](#scenarios)


### <a name="design-principles"></a>Ontwerpprincipes

De volgende ontwerpprincipes [begeleiden][design-principles] het Linkerd-project:

- **Houd het eenvoudig** - Moet gemakkelijk te gebruiken en te begrijpen.

- **Resourcevereisten minimaliseren** - Minimaliseer minimale prestaties en resourcekosten.

- **Just Work** - Breek bestaande toepassingen niet en vereist geen complexe configuratie.


### <a name="capabilities"></a>Functionaliteit

Linkerd biedt de volgende mogelijkheden:

- **Mesh** – ingebouwde foutopsporing, optie

- **Traffic Management** – splitsen, time-outs, nieuwe pogingen, binnendringen

- **Beveiliging** – versleuteling (mTLS), certificaten automatisch geroteerd om de 24 uur

- **Waarneembaarheid** – gouden statistieken, tikken, traceren, serviceprofielen en per routestatistieken, webdashboard met topologiegrafieken, prometheus, grafana


### <a name="scenarios"></a>Scenario's

Linkerd is zeer geschikt voor en voorgesteld voor de volgende scenario's:

- Eenvoudig te gebruiken met alleen de essentiële vereisten voor mogelijkheden

- Lage latentie, lage overhead, met focus op waarneembaarheid en eenvoudig verkeersbeheer


## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Linkerd installeren op Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Linkerd installeren in Azure Kubernetes Service (AKS)][linkerd-install]

U ook de linkerd-functies en architectuur verder verkennen:

- [Linkerd-functies][linkerd-features]
- [Linkerd Architectuur][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md