---
title: Ondersteunde inhoudsindelingen
description: Meer informatie over inhoudsindelingen die worden ondersteund door Azure Container Registry, waaronder containerafbeeldingen die compatibel zijn met Docker, Helm-diagrammen, OCI-afbeeldingen en OCI-artefacten.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247005"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Inhoudsindelingen die worden ondersteund in Azure Container Registry

Gebruik een privéopslagplaats in Azure Container Registry om een van de volgende inhoudsindelingen te beheren. 

## <a name="docker-compatible-container-images"></a>Containerafbeeldingen die compatibel zijn met Docker

De volgende Docker-containerafbeeldingsindelingen worden ondersteund:

* [Docker Image Manifest V2, Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2, Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/) - bevat manifestlijsten waarmee registers multiplatformafbeeldingen kunnen opslaan onder één "image:tag"-verwijzing

## <a name="oci-images"></a>OCI-afbeeldingen

Azure Container Registry ondersteunt afbeeldingen die voldoen aan de [Open Container Initiative (OCI) Image Format Specification](https://github.com/opencontainers/image-spec/blob/master/spec.md). Verpakkingsindelingen omvatten [Singularity Image Format (SIF).](https://github.com/sylabs/sif)

## <a name="oci-artifacts"></a>OCI-artefacten

Azure Container Registry ondersteunt de [OCI Distribution Specification](https://github.com/opencontainers/distribution-spec), een leverancierneutrale, cloud-agnostische spec voor het opslaan, delen, beveiligen en implementeren van containerafbeeldingen en andere inhoudstypen (artefacten). De specificatie maakt het mogelijk een register op te slaan een breed scala van artefacten in aanvulling op container beelden. U gebruikt tooling geschikt voor het artefact te duwen en te trekken artefacten. Zie bijvoorbeeld [Een OCI-artefact pushen en optrekken met behulp van een Azure-containerregister](container-registry-oci-artifacts.md).

Zie de [ORAS-repo (OCI Registry as Storage)](https://github.com/deislabs/oras) en de [OCI Artifacts](https://github.com/opencontainers/artifacts) repo op GitHub voor meer informatie over OCI-artefacten.

## <a name="helm-charts"></a>Helmdiagrammen

Azure Container Registry kan repositories hosten voor [Helm-diagrammen,](https://helm.sh/)een verpakkingsindeling die wordt gebruikt om toepassingen voor Kubernetes snel te beheren en te implementeren. [Helm client](https://docs.helm.sh/using_helm/#installing-helm) versie 2 (2.11.0 of hoger) wordt ondersteund.

## <a name="next-steps"></a>Volgende stappen

* Bekijk hoe u afbeeldingen [kunt pushen en trekken](container-registry-get-started-docker-cli.md) met Azure Container Registry.

* Gebruik [ACR-taken](container-registry-tasks-overview.md) om containerafbeeldingen te maken en te testen. 

* Gebruik de [Moby BuildKit](https://github.com/moby/buildkit) om containers te bouwen en te verpakken in OCI-formaat.

* Stel een [Helm-opslagplaats](container-registry-helm-repos.md) in die wordt gehost in Azure Container Registry. 


