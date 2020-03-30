---
title: Over servicemazen
description: Krijg een overzicht van servicemazen, hun architectuur en mogelijkheden en welke criteria u moet overwegen bij het selecteren van een te implementeren.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594308"
---
# <a name="about-service-meshes"></a>Over servicemazen

Een servicemesh biedt mogelijkheden zoals verkeersbeheer, tolerantie, beleid, beveiliging, sterke identiteit en waarneembaarheid voor uw workloads. Uw toepassing is losgekoppeld van deze operationele mogelijkheden en de servicemesh verplaatst ze uit de toepassingslaag en naar de infrastructuurlaag.

## <a name="scenarios"></a>Scenario's

Dit zijn enkele van de scenario's die kunnen worden ingeschakeld voor uw workloads wanneer u een servicemesh gebruikt:

- **Al het verkeer in het cluster versleutelen** - Schakel wederzijdse TLS tussen opgegeven services in het cluster in. Dit kan worden uitgebreid tot binnendringen en uitwijken bij de netwerkperimeter. Biedt standaard een veilige optie zonder wijzigingen die nodig zijn voor toepassingscode en infrastructuur.

- **Canarische en gefaseerde implementaties** - Geef voorwaarden op voor een subset van verkeer die moet worden doorgestuurd naar een reeks nieuwe services in het cluster. Bij een succesvolle test van de vrijgave van kanarie, verwijder voorwaardelijke routering en geleidelijk verhogen % van al het verkeer naar nieuwe dienst. Uiteindelijk zal al het verkeer worden doorverwezen naar nieuwe service.

- **Verkeersbeheer en -manipulatie** - Maak een beleid voor een service die al het verkeer beperkt tot een versie van een service van een specifieke oorsprong. Of een beleid dat een strategie voor het opnieuw proberen toepast op klassen van fouten tussen opgegeven services. Spiegel live verkeer naar nieuwe versies van services tijdens een migratie of naar foutopsporing problemen. Injecteer fouten tussen services in een testomgeving om tolerantie te testen.

- **Waarneembaarheid** - Krijg inzicht in hoe uw services worden verbonden het verkeer dat tussen hen stroomt. Verkrijg statistieken, logboeken en traces voor al het verkeer in het cluster en ingress/uitgang. Voeg gedistribueerde traceringsmogelijkheden toe aan uw toepassingen.

## <a name="architecture"></a>Architectuur

Een servicemesh bestaat meestal uit een controlevlak en het gegevensvlak.

Het **besturingsvlak** heeft een aantal componenten die het beheer van de servicemesh ondersteunen. Dit omvat meestal een beheerinterface die een gebruikersinterface of een API kan zijn. Er zullen ook meestal componenten zijn die de regel- en beleidsdefinities beheren die bepalen hoe de servicemesh specifieke mogelijkheden moet implementeren. Er zijn ook componenten die aspecten van beveiliging beheren, zoals sterke identiteit en certificaten voor mTLS. Servicemazen hebben meestal ook een basis voor metrische gegevens of waarneembaarheid die statistieken en telemetrie van de workloads verzamelt en verzamelt.

Het **gegevensvlak** bestaat meestal uit een proxy die transparant wordt geïnjecteerd als een zijspan voor uw workloads. Deze proxy is geconfigureerd om al het netwerkverkeer in en uit de pod met uw werkbelasting te beheren. Hierdoor kan de proxy worden geconfigureerd om verkeer te beveiligen via mTLS, dynamisch routeverkeer, beleid toe te passen op verkeer en om statistieken te verzamelen en informatie te traceren. 

![Typische servicemesh-architectuur](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Functionaliteit

Elk van de servicemazen heeft een natuurlijke pasvorm en focus op het ondersteunen van specifieke scenario's, maar u zult meestal merken dat de meeste een aantal, zo niet alle, van de volgende mogelijkheden zullen implementeren.

### <a name="traffic-management"></a>Verkeersmanagement 

- **Protocol** – laag 7 (http, grpc)
- **Dynamic Routing** – conditioneel, weging, spiegelen
- **Veerkracht** – time-outs, nieuwe pogingen, stroomonderbrekers
- **Beleid** – toegangscontrole, tarieflimieten, quota
- **Testen** - foutinjectie

### <a name="security"></a>Beveiliging

- **Versleuteling** – mTLS, certificaatbeheer, externe CA
- **Sterke identiteit** – SPIFFE of iets dergelijks
- **Auth** – authenticatie, autorisatie

### <a name="observability"></a>Observability

- **Metrics** – golden metrics, prometheus, grafana
- **Tracering** - sporen tussen workloads
- **Verkeer** – cluster, binnendringen/uitgang

### <a name="mesh"></a>Mesh

- **Ondersteunde Compute** - Kubernetes, virtuele machines
- **Meerdere cluster -** gateways, federatie

## <a name="selection-criteria"></a>Selectiecriteria

Voordat u een servicemesh selecteert, moet u ervoor zorgen dat u uw vereisten en de redenen voor het installeren van een servicemesh begrijpt. Probeer de volgende vragen te stellen.

- **Is een Ingress Controller voldoende voor mijn behoeften?** - Soms is het voldoende om een vermogen als a/b-testen of het splitsen van het verkeer bij de inbinnendringen te hebben om het vereiste scenario te ondersteunen. Voeg geen complexiteit toe aan uw omgeving zonder opwaartse.

- **Kunnen mijn workloads en omgeving de extra overheadkosten tolereren?** - Alle extra componenten die nodig zijn om de servicemesh te ondersteunen, vereisen extra bronnen zoals cpu en geheugen. Bovendien voegen alle proxy's en bijbehorende beleidscontroles latentie toe aan uw verkeer. Als u workloads hebt die zeer gevoelig zijn voor latentie of die geen extra resources kunnen bieden voor de onderdelen van het servicemesh, moet u deze opnieuw overwegen.

- **Is dit het toevoegen van extra complexiteit onnodig?** - Als de reden voor het installeren van een servicemesh is om een mogelijkheid te krijgen die niet noodzakelijkerwijs essentieel is voor de bedrijfs- of operationele teams, overweeg dan of de extra complexiteit van installatie, onderhoud en configuratie de moeite waard is.

- **Kan dit stapsgewijs worden aangepakt?** - Sommige van de service mazen die veel mogelijkheden bieden kunnen worden aangenomen in een meer incrementele aanpak. Installeer alleen de onderdelen die u nodig hebt om uw succes te garanderen. Zodra u meer vertrouwen en extra mogelijkheden nodig zijn, dan verkennen die. Weersta de drang om *alles* vanaf het begin te installeren.

Als u na zorgvuldige afweging besluit dat u een servicemesh nodig hebt om de vereiste mogelijkheden te bieden, dan is uw volgende beslissing *welke servicemesh?*

Houd rekening met de volgende gebieden en welke van hen zijn het meest afgestemd op uw eisen. Dit zal u begeleiden naar de beste pasvorm voor uw omgeving en werklast. De volgende [stappen](#next-steps) sectie zal u naar meer gedetailleerde informatie over specifieke service mazen en hoe ze in kaart brengen aan deze gebieden.

- **Technisch** - verkeersmanagement, beleid, beveiliging, waarneembaarheid

- **Business** - commerciële ondersteuning, stichting (CNCF), OSS licentie, governance

- **Operationeel** – installatie/upgrades, resourcevereisten, prestatievereisten, integraties (metrics, telemetrie, dashboards, tools, SMI), mixed workloads (Linux en Windows node pools), compute (Kubernetes, virtuele machines), multi-cluster

- **Beveiliging** - auth, identiteit, certificaatbeheer en rotatie, pluggable externe CA


## <a name="next-steps"></a>Volgende stappen

De volgende documentatie bevat meer informatie over servicemazen die u uitproberen op Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Meer informatie over Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Meer informatie over Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Meer informatie over Consul ...][consul-about]

U ook Service Mesh Interface (SMI) verkennen, een standaardinterface voor servicemazen op Kubernetes:

- [Service Mesh-interface (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md