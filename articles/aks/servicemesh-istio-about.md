---
title: Overzicht van Istio
description: Een overzicht van Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593897"
---
# <a name="istio"></a>Istio Istio

## <a name="overview"></a>Overzicht

[Istio][istio] is een volledig uitgerust, aanpasbaar en uitbreidbaar servicemesh.

## <a name="architecture"></a>Architectuur

Istio biedt een dataplane dat [Envoy][envoy-proxy]bestaat uit envoy-gebaseerde zijspannen. Deze intelligente proxy's regelen al het netwerkverkeer in en uit uw meshed apps en workloads.

Het besturingsvlak beheert de configuratie, het beleid en de telemetrie via de volgende [componenten:][what-is-istio]

- **Mixer** - Dwingt toegangsbeheer- en gebruiksbeleid af. Verzamelt telemetrie van de volmachten die in [Prometheus][prometheus]worden geduwd.

- **Pilot** - Biedt servicedetectie en verkeersbeheerbeleid/-configuratie voor de proxy's.

- **Citadel** - Biedt identiteits- en beveiligingsmogelijkheden die mTLS tussen services mogelijk maken.

- **Kombuis** - Abstracts en biedt configuratie aan componenten.

Het volgende architectuurdiagram laat zien hoe de verschillende componenten in het gegevensvlak en het controlevlak met elkaar in wisselwerking staan.


![Overzicht van Istio componenten en architectuur.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Selectiecriteria

Het is belangrijk om de volgende gebieden te begrijpen en te overwegen bij het evalueren van Istio voor uw workloads:

- [Ontwerpdoelen](#design-goals)
- [Functionaliteit](#capabilities)
- [Scenario 's](#scenarios)


### <a name="design-goals"></a>Ontwerpdoelen

De volgende ontwerpdoelen [begeleiden][design-goals] het Istio-project:

- **Maximaliseer transparantie** - Laat adoptie met de minimale hoeveelheid werk toe om echte waarde uit het systeem te halen.

- **Uitbreidbaarheid** - Moet kunnen groeien en zich aanpassen aan veranderende behoeften.

- **Draagbaarheid** - Eenvoudig draaien in verschillende soorten omgevingen - cloud, on-premises.

- **Beleidsuniformiteit** - Consistentie in beleidsdefinitie tussen verschillende bronnen.


### <a name="capabilities"></a>Functionaliteit

Istio biedt de volgende mogelijkheden:

- **Mesh** – gateways (multi-cluster), virtuele machines (mesh-uitbreiding)

- **Traffic Management** – routing, splitting, time-outs, stroomonderbrekers, retries, ingress, uitgang

- **Beleid** – toegangscontrole, tarieflimiet, quotum, aangepaste beleidsadapters

- **Beveiliging** – authenticatie (jwt), autorisatie, encryptie (mTLS), externe CA (HashiCorp Vault)

- **Waarneembaarheid** - gouden metrische gegevens, spiegel, tracering, aangepaste adapters, prometheus, grafana

### <a name="scenarios"></a>Scenario's

Istio is zeer geschikt voor en voorgesteld voor de volgende scenario's:

- Vereisen uitbreidbaarheid en uitgebreide set van mogelijkheden

- Mesh-uitbreiding met vm-gebaseerde workloads

- Multiclusterservicemesh

## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Istio installeren op Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Istio installeren in Azure Kubernetes Service (AKS)][istio-install]

U ook istio-concepten en aanvullende implementatiemodellen verder verkennen:

- [Istio Concepten][what-is-istio]
- [Istio-implementatiemodellen][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
