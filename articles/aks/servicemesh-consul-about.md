---
title: Overzicht van consul
description: Een overzicht van Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594206"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Overzicht

[Consul][consul] is een multi-datacenter bewuste service netwerkoplossing voor het verbinden en beveiligen van diensten via runtime platforms. [Connect][consul-features] is het onderdeel dat servicemesh-mogelijkheden biedt.

## <a name="architecture"></a>Architectuur

Consul biedt standaard een datavliegtuig dat bestaat uit [op envoy][envoy-proxy]gebaseerde [zijspannen.][consul-sidecar] Consul heeft een pluggable proxy architectuur. Deze intelligente proxy's regelen al het netwerkverkeer in en uit uw meshed apps en workloads.

Het besturingsvlak beheert de configuratie en het beleid via de volgende [componenten:][consul-architecture]

- **Server** - Een consul-agent die wordt uitgevoerd in de servermodus die de status van het Consul-cluster behoudt.

- **Klant** - Een consul-agent die in lichtgewicht clientmodus draait. Elke compute-knooppunt moet een clientagent hebben die wordt uitgevoerd. Deze client makelaars configuratie en beleid tussen de workloads en de Consul configuratie. 

Het volgende architectuurdiagram laat zien hoe de verschillende componenten in het gegevensvlak en het controlevlak met elkaar in wisselwerking staan.

![Overzicht van consul componenten en architectuur.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Selectiecriteria

Het is belangrijk om de volgende gebieden te begrijpen en te overwegen bij het evalueren van Consul op uw workloads:

- [Consul-beginselen](#consul-principles)
- [Functionaliteit](#capabilities)
- [Scenario 's](#scenarios)


### <a name="consul-principles"></a>Consul-beginselen

De volgende principes [zijn de leidraad voor][consul-principles] het consul-project:

- **API-Driven** - Codificer alle configuratie en beleid.

- **Run and Connect Anywhere** - Connect workloads over runtime platforms (Kubernetes, VM's, Serverless).

- **Uitbreiden en integreren** - Workloads veilig aansluiten op de infrastructuur.


### <a name="capabilities"></a>Functionaliteit

Consul biedt de volgende mogelijkheden:

- **Mesh** – gateway (multi datacenter), virtuele machines (uit clusterknooppunten), servicesynchronisatie, ingebouwde foutopsporingsoptie

- **Proxy's** - Envoy, ingebouwde proxy, pluggable, l4 proxy beschikbaar voor Windows-workloads

- **Traffic Management** – routing, splitting, resolution

- **Beleid** – intenties, ACL's

- **Beveiliging** – autorisatie, verificatie, versleuteling, op SPIFFE gebaseerde identiteiten, externe CA (Vault), certificaatbeheer en rotatie

- **Waarneembaarheid** – metrics, ui dashboard, prometheus, grafana


### <a name="scenarios"></a>Scenario's

Consul is zeer geschikt voor en voorgesteld voor de volgende scenario's:

- Bestaande door consul verbonden workloads uitbreiden

- Nalevingsvereisten rond certificaatbeheer

- Multiclusterservicenet

- VM-gebaseerde workloads die moeten worden opgenomen in de servicemesh



## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Consul installeren op Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Consul installeren in Azure Kubernetes Service (AKS)][consul-install]

U ook de functies en architectuur van Consul verder verkennen:

- [Consul-functies][consul-features]
- [Consul Architectuur][consul-architecture]
- [Consul - Hoe Connect werkt][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
