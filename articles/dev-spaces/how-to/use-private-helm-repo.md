---
title: Een privé-Helm-opslagplaats gebruiken in Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Gebruik een privé-Helm-opslagplaats in een Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240475"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Een privé-Helm-opslagplaats gebruiken in Azure Dev Spaces

[Helm][helm] is package manager voor Kubernetes. Helm gebruikt een [grafiekindeling][helm-chart] om afhankelijkheden te verpakken. Helmdiagrammen worden opgeslagen in een opslagplaats, die openbaar of privé kan zijn. Azure Dev Spaces haalt alleen Helm-diagrammen op uit openbare opslagplaatsen wanneer u uw toepassing uitvoert. In gevallen waarin de Helm-repository privé is of Azure Dev Spaces er geen toegang toe heeft, u een grafiek vanuit die opslagplaats rechtstreeks aan uw toepassing toevoegen. Als u de grafiek rechtstreeks toevoegt, kan Azure Dev Spaces uw toepassing uitvoeren zonder dat u toegang hoeft te krijgen tot de privé-Helm-opslagplaats.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Voeg de private Helm-opslagplaats toe aan uw lokale machine

Gebruik [helm repo toevoegen][helm-repo-add] en roer [repo update][helm-repo-update] om toegang te krijgen tot de prive Helm repository van uw lokale machine.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>De grafiek toevoegen aan uw toepassing

Navigeer naar de map van `azds prep`uw project en voer uit.

```cmd
azds prep --enable-ingress
```

> [!TIP]
> De `prep` opdracht probeert [een Dockerfile- en Helm-diagram](../how-dev-spaces-works-prep.md#prepare-your-code) voor uw project te genereren. Azure Dev Spaces gebruikt deze bestanden om uw code te bouwen en uit te voeren, maar u deze bestanden wijzigen als u de manier waarop het project is opgebouwd en uitgevoerd wilt wijzigen.

Maak een [requirements.yaml-bestand][helm-requirements] met uw grafiek in de grafiekmap van uw toepassing. Als uw toepassing bijvoorbeeld *app1*wordt genoemd, maakt u *grafieken/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navigeer naar de grafiekmap van uw toepassing en gebruik [de afhankelijkheidsupdate van het roer][helm-dependency-update] om de afhankelijkheden van het helm voor uw toepassing bij te werken en download de grafiek vanuit de privéopslagplaats.

```cmd
helm dependency update
```

Controleer of er een *grafiekensubmap* met een *tgz-bestand* is toegevoegd aan de grafiekmap van uw toepassing. *Grafieken/app1/grafieken/mychart-0.1.0.tgz*.

De grafiek van uw privé-Helm-opslagplaats is gedownload en toegevoegd aan uw project. Verwijder het *bestand requirements.yaml,* zodat Dev Spaces niet probeert deze afhankelijkheid bij te werken.

## <a name="run-your-application"></a>Uw toepassing uitvoeren

Navigeer naar de hoofdmap van `azds up` uw project en voer deze uit om te controleren of uw toepassing in uw ontwikkelaarsruimte wordt uitgevoerd.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Helm en hoe het werkt.][helm]

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
