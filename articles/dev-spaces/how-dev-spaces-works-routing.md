---
title: Hoe routering werkt met Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen die Azure Dev Spaces van stroom voorstaan en hoe routering werkt
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: e9bc1875c053335da6a8e2603406bcdb34a6dd04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241385"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Hoe routering werkt met Azure Dev Spaces

Azure Dev Spaces biedt u meerdere manieren om Kubernetes-toepassingen snel te herhalen en te debuggen en samen te werken met uw team op een AKS-cluster (Azure Kubernetes Service). Zodra uw project in een dev-ruimte wordt uitgevoerd, biedt Azure Dev Spaces extra netwerk- en routeringsmogelijkheden voor uw project.

In dit artikel wordt beschreven hoe routering werkt met Dev Spaces.

## <a name="how-routing-works"></a>Hoe routering werkt

Een dev ruimte is gebouwd op de top van AKS en maakt gebruik van dezelfde [netwerkconcepten.](../aks/concepts-network.md) Azure Dev Spaces heeft ook een gecentraliseerde *ingressmanager-service* en implementeert zijn eigen Ingress-controller in het AKS-cluster. De *ingressmanager-service* bewaakt AKS-clusters met dev-ruimten en breidt de Azure Dev Spaces Ingress-controller in het cluster uit met Ingress-objecten voor routering naar toepassingspods. De devspaces-proxycontainer in elke `azds-route-as` pod voegt een HTTP-header voor HTTP-verkeer toe aan een dev-ruimte op basis van de URL. Een aanvraag voor de *http://azureuser.s.default.serviceA.fedcba09...azds.io* URL krijgt bijvoorbeeld `azds-route-as: azureuser`een HTTP-header met . De devspaces-proxy container voegt `azds-route-as` geen koptekst toe als deze al aanwezig is.

Wanneer een HTTP-aanvraag wordt ingediend bij een service van buiten het cluster, gaat de aanvraag naar de Ingress-controller. De Ingress-controller leidt de aanvraag rechtstreeks naar de juiste pod op basis van de objecten en regels van Ingress. De devspaces-proxy container in de pod `azds-route-as` ontvangt de aanvraag, voegt de koptekst toe op basis van de URL en stuurt vervolgens het verzoek naar de toepassingscontainer.

Wanneer een HTTP-aanvraag wordt gedaan naar een service vanuit een andere service binnen het cluster, gaat de aanvraag eerst via de devspaces-proxy-container van de aanroepende service. De container devspaces-proxy kijkt naar de `azds-route-as` HTTP-aanvraag en controleert de koptekst. Op basis van de koptekst zoekt de devspaces-proxycontainer het IP-adres op van de service die is gekoppeld aan de kopwaarde. Als er een IP-adres wordt gevonden, leidt de devspaces-proxycontainer het verzoek om naar dat IP-adres. Als er geen IP-adres wordt gevonden, leidt de container devspaces-proxy de aanvraag door naar de bovenliggende toepassingscontainer.

De *toepassingenserviceA* en *serviceB* worden bijvoorbeeld geïmplementeerd in een bovenliggende dev-ruimte die *standaard wordt*genoemd. *serviceA* vertrouwt op *serviceB* en doet HTTP-oproepen. Azure User maakt een onderliggende dev-ruimte op basis van de *standaardruimte* die *azureuser*wordt genoemd. Azure User implementeert ook hun eigen versie van *serviceA* naar hun onderliggende ruimte. Wanneer een verzoek *http://azureuser.s.default.serviceA.fedcba09...azds.io*wordt ingediend om :

![Azure Dev Spaces-routering](media/how-dev-spaces-works/routing.svg)

1. De Ingress-controller zoekt het IP op voor de pod die is gekoppeld aan de URL, die *serviceA.azureuser*is.
1. De Ingress-controller vindt het IP-adres voor de pod in de dev-ruimte van Azure User en leidt de aanvraag door naar de *pod serviceA.azureuser.*
1. De devspaces-proxycontainer in de pod *serviceA.azureuser* ontvangt de aanvraag en voegt deze toe `azds-route-as: azureuser` als een HTTP-header.
1. De container devspaces-proxy in de pod *serviceA.azureuser* leidt de aanvraag door naar de *serviceA-toepassingscontainer* in de *pod serviceA.azureuser.*
1. De *serviceA-toepassing* in de *serviceA.azureuser-pod* maakt een call-to-serviceB . *serviceB* De *serviceA-toepassing* bevat ook `azds-route-as` code om de bestaande `azds-route-as: azureuser`header te behouden, wat in dit geval .
1. De devspaces-proxycontainer in de pod *serviceA.azureuser* ontvangt de aanvraag en zoekt het `azds-route-as` IP-adres van *serviceB* op op basis van de waarde van de koptekst.
1. De devspaces-proxy-container in de pod *serviceA.azureuser* vindt geen IP voor *serviceB.azureuser*.
1. De devspaces-proxy-container in de pod *serviceA.azureuser* zoekt het IP op naar *serviceB* in de bovenliggende ruimte, namelijk *serviceB.default*.
1. De devspaces-proxycontainer in de pod *serviceA.azureuser* vindt het IP voor *serviceB.default* en leidt de aanvraag door naar de *pod serviceB.default.*
1. De container devspaces-proxy in de pod *serviceB.default* ontvangt de aanvraag en stuurt de aanvraag door naar de *serviceB-toepassingscontainer* in de *pod serviceB.default.*
1. De *serviceB-toepassing* in de *pod serviceB.default* retourneert een antwoord op de *pod serviceA.azureuser.*
1. De container devspaces-proxy in de pod *serviceA.azureuser* ontvangt het antwoord en stuurt het antwoord naar de *serviceA-toepassingscontainer* in de *pod serviceA.azureuser.*
1. De *serviceA-toepassing* ontvangt het antwoord en retourneert vervolgens zijn eigen antwoord.
1. De container devspaces-proxy in de pod *serviceA.azureuser* ontvangt het antwoord van de *serviceA-toepassingscontainer* en leidt het antwoord naar de oorspronkelijke beller buiten het cluster.

Al het andere TCP-verkeer dat geen HTTP is, gaat ongewijzigd door de Ingress-controller en devspaces-proxy-containers.

## <a name="sharing-a-dev-space"></a>Een dev-ruimte delen

Wanneer u met een team werkt, u [een dev-ruimte delen in een heel team](how-to/share-dev-spaces.md) en afgeleide dev-ruimten maken. Een dev-ruimte kan worden gebruikt door iedereen met inzender toegang tot de resourcegroep van de dev-ruimte.

U ook een nieuwe dev-ruimte maken die is afgeleid van een andere dev-ruimte. Wanneer u een afgeleide dev-ruimte maakt, wordt het *azds.io/parent-space=PARENT-SPACE-NAME* label toegevoegd aan de naamruimte van de afgeleide dev-ruimte. Ook worden alle toepassingen van de bovenliggende dev-ruimte gedeeld met de afgeleide dev-ruimte. Als u een bijgewerkte versie van een toepassing implementeert in de afgeleide dev-ruimte, bestaat deze alleen in de afgeleide dev-ruimte en blijft de bovenliggende dev-ruimte ongewijzigd. U een maximum van drie niveaus van afgeleide dev ruimtes of *grootouder* ruimtes.

De afgeleide dev-ruimte zal ook op intelligente wijze aanvragen routeren tussen de eigen toepassingen en de toepassingen die worden gedeeld door de bovenliggende toepassing. De routering werkt door te proberen om aanvraag route naar een toepassing in de afgeleide dev ruimte en terug te vallen naar de gedeelde toepassing van de bovenliggende dev ruimte. De routering valt terug naar de gedeelde toepassing in de grootouderruimte als de toepassing zich niet in de bovenliggende ruimte bevindt.

Bijvoorbeeld:
* De standaardinstelling dev *space* heeft applications *serviceA* en *serviceB*.
* De *azureuser* voor de v-ruimte is afgeleid van *standaard*.
* Een bijgewerkte versie van *serviceA* wordt geïmplementeerd in *azureuser*.

Bij het gebruik van *azureuser*worden alle aanvragen voor *serviceA* doorgestuurd naar de bijgewerkte versie in *azureuser.* Een verzoek om *serviceB* wordt eerst doorgestuurd naar de *azureuser-versie* van *serviceB.* Aangezien het niet bestaat, zal het worden doorgestuurd naar de *standaardversie* van *serviceB*. Als de *azureuserversie* van *serviceA* wordt verwijderd, worden alle aanvragen voor *serviceA* teruggezet naar het gebruik van de *standaardversie* van *serviceA*.

## <a name="next-steps"></a>Volgende stappen

Zie Hoe het verbinden van [uw ontwikkelmachine met uw ontwikkelruimte werkt,][how-it-works-connect] [hoe externe debuggen van uw code met Azure Dev Spaces werkt][how-it-works-remote-debugging]en [GitHub Actions & Azure Kubernetes Service][pr-flow]voor een voorbeeld van hoe Azure Dev Spaces routering gebruikt om snel iteratie en ontwikkeling te bieden.

Zie de [teamontwikkeling in Azure Dev Spaces][quickstart-team] snel van start te gaan om aan de slag te gaan met routering met Azure Dev Spaces voor teamontwikkeling.

[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-connect]: how-dev-spaces-works-connect.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[quickstart-team]: quickstart-team-development.md
[troubleshooting]: troubleshooting.md