---
title: Beheerde container registers
description: Kennismaking met de Azure Container Registry-service, waarmee u cloudgebaseerde, beheerde en persoonlijke Docker-registers kunt maken.
author: stevelas
ms.topic: overview
ms.date: 12/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 863b93497505443b79f41f580150a4dbf790a6f2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445717"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Inleiding tot privé-Docker-containerregisters in Azure

Azure Container Registry is een beheerde, persoonlijke docker-register service op basis van het open source-docker-REGI ster 2,0. Azure-container registers maken en onderhouden om uw persoonlijke docker-container installatie kopieën en gerelateerde artefacten op te slaan en te beheren.

Gebruik Azure-container registers met uw bestaande pijp lijnen voor het ontwikkelen en implementeren van containers, of gebruik Azure Container Registry taken om container installatie kopieën in azure te maken. Bouw op aanvraag of volledig Automatiseer builds met triggers, zoals het door voeren van de bron code en updates van de basis installatie kopie.

Zie voor meer informatie over docker-en register concepten het [docker-overzicht](https://docs.docker.com/engine/docker-overview/) en [over registers, opslag plaatsen en installatie kopieën](container-registry-concepts.md).

## <a name="use-cases"></a>Use cases

Haal installatiekopieën op vanuit een Azure-containerregister en push ze naar verschillende implementatiedoelen:

* **Schaalbare indelingssystemen** die toepassingen in een container beheren in hostclusters, waaronder [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) en [Docker Swarm](https://docs.docker.com/swarm/).
* **Azure-services** die het bouwen en uitvoeren van toepassingen op schaal ondersteunen, waaronder [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) en andere.

Ontwikkelaars kunnen ook naar een containerregister pushen als onderdeel van een ontwikkelingswerkstroom met containers. Als u bijvoorbeeld een container register wilt richten op een continue integratie-en leverings hulp programma, zoals [Azure-pijp lijnen](/azure/devops/pipelines/ecosystems/containers/acr-template) of [Jenkins](https://jenkins.io/).

Configureer ACR-taken zodanig dat toepassings installatie kopieën automatisch opnieuw worden opgebouwd wanneer hun basis installatie kopieën worden bijgewerkt, of Automatiseer installatie kopieën wanneer uw team code doorvoert naar een Git-opslag plaats. Maak taken met meerdere stappen om het bouwen, testen en bijwerken van meerdere container installatie kopieën parallel in de cloud te automatiseren.

Azure biedt hulp middelen zoals Azure Command-Line Interface, Azure Portal en API-ondersteuning voor het beheren van uw Azure-container registers. Installeer eventueel de [docker-extensie voor Visual Studio code](https://code.visualstudio.com/docs/azure/docker) en de [Azure-account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) extensie om met uw Azure-container registers te werken. Pull-en push-installatie kopieën naar een Azure container Registry, of voer ACR-taken uit, allemaal in Visual Studio code.

## <a name="key-features"></a>Belangrijke functies

* **Register-sku's** : Maak een of meer container registers in uw Azure-abonnement. Registers zijn beschikbaar in drie Sku's: [Basic, Standard en Premium](container-registry-skus.md), die allemaal ondersteuning bieden voor integratie van webhooks, register verificatie met Azure Active Directory en functionaliteit voor verwijderen. Maak een register op dezelfde Azure-locatie als uw implementaties om te profiteren van lokale opslag dichtbij in het netwerk van uw containerinstallatiekopieën. Gebruik de [geo-replicatie](container-registry-geo-replication.md)functie van Premium-registers voor geavanceerde replicatie- en distributiescenario's voor containerinstallatiekopieën. 

* **Beveiliging en toegang** : u meldt zich aan bij een REGI ster met behulp van de Azure CLI of de standaard `docker login` opdracht. Azure Container Registry container installatie kopieën overdraagt via HTTPS en TLS ondersteunt om client verbindingen te beveiligen. 

  > [!IMPORTANT]
  > Vanaf 13 januari 2020 moeten voor Azure Container Registry alle beveiligde verbindingen van servers en toepassingen worden gebruikt om TLS 1,2 te gebruiken. Ondersteuning voor TLS 1,0 en 1,1 wordt buiten gebruik gesteld.

  U kunt [toegang beheren](container-registry-authentication.md) tot een containerregister met behulp van een Azure-identiteit, een door Azure Active Directory ondersteunde [service-principal](../active-directory/develop/app-objects-and-service-principals.md) of een opgegeven beheeraccount. Gebruik op rollen gebaseerd toegangs beheer (RBAC) voor het toewijzen van gebruikers of systemen met een nauw keurige machtiging voor een REGI ster.

  Beveiligings functies van de Premium-SKU bevatten [inhoud vertrouwen](container-registry-content-trust.md) voor het ondertekenen van installatie kopieën van tags en [firewalls en virtuele netwerken (preview)](container-registry-vnet.md) om de toegang tot het REGI ster te beperken. Azure Security Center optioneel kan worden geïntegreerd met Azure Container Registry om [afbeeldingen te scannen](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) wanneer een installatie kopie naar een REGI ster wordt gepusht.

* **Ondersteunde installatie kopieën en artefacten** : gegroepeerd in een opslag plaats is elke afbeelding een alleen-lezen moment opname van een met docker compatibele container. Azure-containerregisters kunnen zowel Windows- als Linux-installatiekopieën bevatten. U beheert de namen van de installatiekopieën voor al uw containerimplementaties. Gebruik standaard-[Docker-opdrachten](https://docs.docker.com/engine/reference/commandline/) om installatiekopieën naar een opslagplaats te pushen of een installatiekopie uit een opslagplaats op te halen. Naast docker-container installatie kopieën worden met Azure Container Registry [gerelateerde inhouds indelingen](container-registry-image-formats.md) opgeslagen, zoals [helm-grafieken](container-registry-helm-repos.md) en installatie kopieën die zijn gemaakt in de indeling van het [opening container Initiative (OCI)-afbeelding](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Automatische installatie kopieën-builds** : gebruik [Azure container Registry taken](container-registry-tasks-overview.md) (ACR taken) om het bouwen, testen, pushen en implementeren van installatie kopieën in azure te stroom lijnen. Gebruik bijvoorbeeld ACR-taken om uw ontwikkeling binnenste lus uit te breiden naar de Cloud door `docker build` bewerkingen over te dragen naar Azure. Configureer buildtaken om uw container-OS- en frameworkpatchingpijplijn te automatiseren en automatisch installatiekopieën te maken wanneer uw team code met bronbeheer doorvoert.

  [Taken met meerdere stappen](container-registry-tasks-overview.md#multi-step-tasks) bieden taak definitie en uitvoering op basis van een stap voor het bouwen, testen en bijwerken van container installatie kopieën in de Cloud. Taakstappen definiëren afzonderlijke ontwikkel- en pushbewerkingen voor containerinstallatiekopieën. Ze kunnen ook de uitvoering definiëren van een of meer containers, waarbij elke stap de container als uitvoeringsomgeving gebruikt.

## <a name="next-steps"></a>Volgende stappen

* [Een containerregister maken met Azure Portal](container-registry-get-started-portal.md)
* [Een containerregister maken met de Azure-CLI](container-registry-get-started-azure-cli.md)
* [Container builds en onderhoud automatiseren met ACR-taken](container-registry-tasks-overview.md)