---
title: Hoe het opzetten van een cluster voor Azure Dev Spaces werkt
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft hoe het opzetten van een Azure Kubernetes Service-cluster voor Azure Dev Spaces werkt
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241723"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Hoe het opzetten van een cluster voor Azure Dev Spaces werkt

Azure Dev Spaces biedt u meerdere manieren om Kubernetes-toepassingen snel te herhalen en te debuggen en samen te werken met uw team op een AKS-cluster (Azure Kubernetes Service). Een manier is om Azure Dev Spaces in te schakelen op uw AKS-cluster, zodat u [services rechtstreeks op uw cluster][how-it-works-up] uitvoeren en extra [netwerk- en routeringsmogelijkheden][how-it-works-routing]gebruiken. In dit artikel wordt beschreven wat er gebeurt wanneer u uw cluster voorbereidt en Azure Dev Spaces inschakelt.

## <a name="prepare-your-aks-cluster"></a>Uw AKS-cluster voorbereiden

Als u uw AKS-cluster wilt voorbereiden op Dev Spaces, controleert u of uw AKS-cluster zich in een regio [bevindt die wordt ondersteund door Azure Dev Spaces][supported-regions] en gebruikt u Kubernetes 1.10.3 of hoger. Als u Azure Dev Spaces op uw cluster wilt inschakelen vanuit de Azure-portal, navigeert u naar uw cluster, klikt u op *Dev-spaties,* *wijzigt u Dev Spaces* gebruiken in *Ja*en klikt u op *Opslaan*. U Azure Dev Spaces ook inschakelen `az aks use-dev-spaces`vanuit azure cli door.

Zie voor een voorbeeld van het opzetten van een AKS-cluster voor Dev Spaces de [teamontwikkeling snel van start.][quickstart-team]

Wanneer Azure Dev Spaces is ingeschakeld op uw AKS-cluster, installeert het de controller voor uw cluster. De controller bevindt zich buiten uw AKS-cluster. Het stuurt het gedrag en de communicatie tussen de client-side tooling en de AKS cluster. Zodra deze is ingeschakeld, u communiceren met de controller met behulp van de client-side tooling.

De controller voert de volgende acties uit:

* Beheert het maken en selecteren van dev-ruimte.
* Installeert de Helm-grafiek van uw toepassing en maakt Kubernetes-objecten.
* Hiermee bouwt u de containerafbeelding van uw toepassing.
* Implementeert uw toepassing op AKS.
* Bouwt incrementeel en start opnieuw wanneer uw broncode verandert.
* Beheert logboeken en HTTP-sporen.
* Doorsturen stdout en stderr naar de client-side tooling.
* Hiermee configureert u routering voor toepassingen binnen een ruimte en voor bovenliggende en onderliggende ruimten.

De controller is een afzonderlijke Azure-bron buiten uw cluster en doet het volgende voor resources in uw cluster:

* Hiermee maakt of wijst u een Kubernetes-naamruimte aan die u als dev-ruimte wilt gebruiken.
* Hiermee verwijdert u alle Kubernetes-naamruimte met de naam *azds*, als deze bestaat, en wordt een nieuwe gemaakt.
* Implementeert een Kubernetes webhook-configuratie.
* Implementeert een webhook-toegangsserver.

Het maakt gebruik van dezelfde serviceprincipal die uw AKS-cluster gebruikt om serviceaanteroepen naar andere Azure Dev Spaces-componenten.

![Azure Dev Spaces bereidt cluster voor](media/how-dev-spaces-works/prepare-cluster.svg)

Om Azure Dev Spaces te kunnen gebruiken, moet er ten minste één dev-ruimte zijn. Azure Dev Spaces gebruikt Kubernetes-naamruimten binnen uw AKS-cluster voor dev-ruimten. Wanneer een controller wordt geïnstalleerd, wordt u gevraagd om een nieuwe Kubernetes-naamruimte te maken of een bestaande naamruimte te kiezen die u als eerste dev-ruimte wilt gebruiken. Standaard biedt de controller aan om de bestaande *standaard* Kubernetes-naamruimte te upgraden naar uw eerste dev-ruimte.

Wanneer een naamruimte wordt aangewezen als een dev-ruimte, voegt de controller het *azds.io/space=true* label toe aan die naamruimte om deze als een dev-ruimte te identificeren. De initiële dev-ruimte die u maakt of aanwijst, wordt standaard geselecteerd nadat u uw cluster hebt voorbereid. Wanneer een spatie is geselecteerd, wordt deze gebruikt door Azure Dev Spaces voor het maken van nieuwe workloads.

U de client-side tooling gebruiken om nieuwe dev-ruimten te maken en bestaande dev-ruimten te verwijderen. Vanwege een beperking in Kubernetes kan de *standaard* dev-ruimte niet worden verwijderd. De controller verwijdert ook bestaande Kubernetes-naamruimten met de `azds` naam *azds* om conflicten met de opdracht die wordt gebruikt door de client-side tooling te voorkomen.

De Kubernetes webhook-toegangsserver wordt gebruikt om pods met drie containers te injecteren tijdens de implementatie voor instrumentatie: een devspaces-proxy-container, een devspaces-proxy-init-container en een devspaces-build container. **Alle drie deze containers worden uitgevoerd met root-toegang op uw AKS-cluster.** Ze gebruiken ook dezelfde serviceprincipal die uw AKS-cluster gebruikt om serviceaanteroepen naar andere Azure Dev Spaces-componenten.

![Azure Dev Spaces Kubernetes webhook-toegangsserver](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

De devspaces-proxy container is een sidecar container die al het TCP-verkeer naar en uit de toepassingscontainer verwerkt en helpt bij het uitvoeren van routing. De container devspaces-proxy leidt HTTP-berichten om als bepaalde spaties worden gebruikt. Het kan bijvoorbeeld helpen bij het routeren van HTTP-berichten tussen toepassingen in bovenliggende en onderliggende ruimten. Al het niet-HTTP-verkeer gaat ongewijzigd door devspaces-proxy. De devspaces-proxy container registreert ook alle inkomende en uitgaande HTTP-berichten en stuurt ze naar de client-side tooling als traces. Deze sporen kunnen vervolgens worden bekeken door de ontwikkelaar om het gedrag van de toepassing te inspecteren.

De devspaces-proxy-init-container is een [init-container](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) die extra routeringsregels toevoegt op basis van de ruimtehiërarchie aan de container van uw toepassing. Het voegt routeringsregels toe door het *bestand en* iptables-bestand van de toepassingscontainer bij te werken voordat deze wordt gestart. De updates voor */etc/resolv.conf* zorgen voor DNS-oplossing van services in bovenliggende ruimten. De iptables-configuratie-updates zorgen ervoor dat al het TCP-verkeer in en uit de container van de toepassing wordt gerouteerd via devspaces-proxy. Alle updates van devspaces-proxy-init gebeuren naast de regels die Kubernetes toevoegt.

De devspaces-build container is een init container en heeft de project broncode en Docker socket gemonteerd. Met de broncode van het project en de toegang tot Docker kan de toepassingscontainer rechtstreeks door de pod worden gebouwd.

> [!NOTE]
> Azure Dev Spaces gebruikt hetzelfde knooppunt om de container van uw toepassing te bouwen en uit te voeren. Azure Dev Spaces heeft daarom geen extern containerregister nodig voor het bouwen en uitvoeren van uw toepassing.

De Kubernetes webhook toegangsserver luistert naar elke nieuwe pod die is gemaakt in het AKS-cluster. Als die pod wordt geïmplementeerd in *azds.io/space=true* een naamruimte met het azds.io/space=true-label, injecteert deze pod met de extra containers. De devspaces-build container wordt alleen geïnjecteerd als de container van de toepassing wordt uitgevoerd met behulp van de client-side tooling.

Zodra u uw AKS-cluster hebt voorbereid, u de client-side tooling gebruiken om uw code voor te bereiden en uit te voeren in uw dev-ruimte.

## <a name="client-side-tooling"></a>Tooling aan de clientzijde

Met de client-side tooling kan de gebruiker:
* Genereer een Dockerfile-, Helm-diagram en Azure Dev Spaces-configuratiebestand voor de toepassing.
* Bovenliggende en onderliggende dev-ruimten maken.
* Vertel de controller om uw toepassing te bouwen en te starten.

Terwijl uw toepassing wordt uitgevoerd, de client-side tooling ook:
* Ontvangt en toont stdout en stderr van uw toepassing die in AKS wordt uitgevoerd.
* Gebruikt [port forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) om webtoegang tot uw\/toepassing toe te staan met http: /localhost.
* Hiermee wordt een foutopsporing gekoppeld aan uw hardlooptoepassing in AKS.
* Hiermee synchroniseert u de broncode naar uw dev-ruimte wanneer een wijziging wordt gedetecteerd voor incrementele builds, waardoor u snel kan worden iteratie.
* Hiermee u uw ontwikkelaarsmachine rechtstreeks verbinden met uw AKS-cluster.

U de client-side tooling van de `azds` opdrachtregel gebruiken als onderdeel van de opdracht. U ook de client-side tooling gebruiken met:

* Visual Studio Code met de [Azure Dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio met [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Volgende stappen

Zie Hoe het [voorbereiden van een project voor Azure Dev Spaces werkt][how-it-works-prep]voor meer informatie over het gebruik van de tooling aan clientzijde om uw code in uw ontwikkelaarsruimte voor te bereiden en uit te voeren.

Zie de [teamontwikkeling in Azure Dev Spaces][quickstart-team] snel van start om aan de slag te gaan met Azure Dev Spaces voor teamontwikkeling.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md