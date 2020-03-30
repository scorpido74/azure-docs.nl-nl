---
title: Beheerde containerregisters
description: Kennismaking met de Azure Container Registry-service, waarmee u cloudgebaseerde, beheerde en persoonlijke Docker-registers kunt maken.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 1992a2a63d16a955d136459f5dbaece7df815c71
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77132036"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Inleiding tot privé-Docker-containerregisters in Azure

Azure Container Registry is een beheerde, private Docker-registerservice op basis van het open-source Docker Registry 2.0. Azure-containerregisters maken en onderhouden om uw privé Docker-containerafbeeldingen en gerelateerde artefacten op te slaan en te beheren.

Gebruik Azure-containerregisters met uw bestaande containerontwikkelings- en implementatiepijplijnen of gebruik Azure Container Registry Tasks om containerafbeeldingen in Azure te maken. Bouw op aanvraag, of automatiseer builds volledig met triggers zoals broncodecommits en basisimage-updates.

Zie het [docker-overzicht](https://docs.docker.com/engine/docker-overview/) en [Over registers, opslagplaatsen en afbeeldingen](container-registry-concepts.md)voor meer informatie over docker- en registerconcepten.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Haal installatiekopieën op vanuit een Azure-containerregister en push ze naar verschillende implementatiedoelen:

* **Schaalbare indelingssystemen** die toepassingen in een container beheren in hostclusters, waaronder [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) en [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-services** die het bouwen en uitvoeren van toepassingen op schaal ondersteunen, waaronder [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) en andere.

Ontwikkelaars kunnen ook naar een containerregister pushen als onderdeel van een ontwikkelingswerkstroom met containers. Target bijvoorbeeld een containerregister vanuit een continue integratie- en leveringstool, zoals [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) of [Jenkins.](https://jenkins.io/)

Configureer ACR-taken om toepassingsafbeeldingen automatisch opnieuw op te bouwen wanneer hun basisafbeeldingen worden bijgewerkt of automatiseer afbeeldingsbuilds wanneer uw team code vastlegt aan een Git-opslagplaats. Maak taken in meerdere stappen om het bouwen, testen en patchen van meerdere containerafbeeldingen parallel in de cloud te automatiseren.

Azure biedt tooling, waaronder Azure Command-Line Interface, Azure portal en API-ondersteuning om uw Azure-containerregisters te beheren. Installeer eventueel de [Docker-extensie voor Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-accountextensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) om te werken met uw Azure-containerregisters. Afbeeldingen naar een Azure-containerregister trekken en pushen of ACR-taken uitvoeren, allemaal binnen Visual Studio Code.

## <a name="key-features"></a>Belangrijke functies

* **Register SKU's** - Maak een of meer containerregisters in uw Azure-abonnement. Registers zijn beschikbaar in drie SKU's: [Basic, Standard en Premium,](container-registry-skus.md)die elk webhook-integratie, registerverificatie met Azure Active Directory en verwijderfunctionaliteit ondersteunen. Maak een register op dezelfde Azure-locatie als uw implementaties om te profiteren van lokale opslag dichtbij in het netwerk van uw containerinstallatiekopieën. Gebruik de [geo-replicatie](container-registry-geo-replication.md)functie van Premium-registers voor geavanceerde replicatie- en distributiescenario's voor containerinstallatiekopieën. 

* **Beveiliging en toegang** : u meldt zich aan bij `docker login` een register met de Azure CLI of de standaardopdracht. Azure Container Registry brengt containerafbeeldingen over naar HTTPS en ondersteunt TLS om clientverbindingen te beveiligen. 

  > [!IMPORTANT]
  > Vanaf 13 januari 2020 vereist Azure Container Registry dat alle beveiligde verbindingen van servers en toepassingen TLS 1.2 gebruiken. Schakel TLS 1.2 in met een recente dockerclient (versie 18.03.0 of hoger). Ondersteuning voor TLS 1.0 en 1.1 wordt buiten gebruik gesteld. 

  U kunt [toegang beheren](container-registry-authentication.md) tot een containerregister met behulp van een Azure-identiteit, een door Azure Active Directory ondersteunde [service-principal](../active-directory/develop/app-objects-and-service-principals.md) of een opgegeven beheeraccount. Gebruik rbac (role-based access control) om gebruikers of systemen fijnmazige machtigingen toe te wijzen aan een register.

  Beveiligingsfuncties van de Premium SKU omvatten [contentvertrouwen](container-registry-content-trust.md) voor het ondertekenen van [afbeeldingstags en firewalls en virtuele netwerken (preview)](container-registry-vnet.md) om de toegang tot het register te beperken. Azure Security Center integreert optioneel met Azure Container Registry om afbeeldingen te [scannen](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) wanneer een afbeelding naar een register wordt gepusht.

* **Ondersteunde afbeeldingen en artefacten** - Gegroepeerd in een opslagplaats is elke afbeelding een alleen-lezen momentopname van een Docker-compatibele container. Azure-containerregisters kunnen zowel Windows- als Linux-installatiekopieën bevatten. U beheert de namen van de installatiekopieën voor al uw containerimplementaties. Gebruik standaard-[Docker-opdrachten](https://docs.docker.com/engine/reference/commandline/) om installatiekopieën naar een opslagplaats te pushen of een installatiekopie uit een opslagplaats op te halen. Naast Docker-containerafbeeldingen slaat Azure Container Registry [gerelateerde inhoudsindelingen](container-registry-image-formats.md) op, zoals [Helmdiagrammen](container-registry-helm-repos.md) en afbeeldingen die zijn gebouwd volgens de [Afbeeldingsformaatspecificatie (Open Container Initiative).](https://github.com/opencontainers/image-spec/blob/master/spec.md)

* **Geautomatiseerde imagebuilds** - Gebruik [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) om het bouwen, testen, pushen en implementeren van afbeeldingen in Azure te stroomlijnen. Gebruik bijvoorbeeld ACR-taken om uw ontwikkelinnerloop uit te `docker build` breiden naar de cloud door bewerkingen naar Azure te ontladen. Configureer buildtaken om uw container-OS- en frameworkpatchingpijplijn te automatiseren en automatisch installatiekopieën te maken wanneer uw team code met bronbeheer doorvoert.

  [Taken in meerdere stappen](container-registry-tasks-overview.md#multi-step-tasks) bieden stapsgewijze taakdefinitie en -uitvoering voor het maken, testen en patchen van containerafbeeldingen in de cloud. Taakstappen definiëren afzonderlijke ontwikkel- en pushbewerkingen voor containerinstallatiekopieën. Ze kunnen ook de uitvoering definiëren van een of meer containers, waarbij elke stap de container als uitvoeringsomgeving gebruikt.

## <a name="next-steps"></a>Volgende stappen

* [Een containerregister maken met Azure Portal](container-registry-get-started-portal.md)
* [Een containerregister maken met de Azure CLI](container-registry-get-started-azure-cli.md)
* [Containerbuilds en onderhoud automatiseren met ACR-taken](container-registry-tasks-overview.md)