---
title: Upgrade ingress controller met Helm
description: In dit artikel vindt u informatie over het upgraden van een Application Gateway Ingress met Helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795893"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Een upgraden uitvoeren van de controller voor inkomend verkeer van Application Gateway met behulp van Helm 

De Azure Application Gateway Ingress Controller voor Kubernetes (AGIC) kan worden geüpgraded met behulp van een Helm-repository die wordt gehost op Azure Storage.

Voordat we met de upgradeprocedure beginnen, moet u ervoor zorgen dat u de vereiste opslagplaats hebt toegevoegd:

- Bekijk uw momenteel toegevoegde Helm-opslagplaatsen met:

    ```bash
    helm repo list
    ```

- Voeg de AGIC repo toe met:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Upgraden

1. Vernieuw de AGIC Helm repository om de nieuwste release te krijgen:

    ```bash
    helm repo update
    ```

1. Bekijk de beschikbare `application-gateway-kubernetes-ingress` versies van de grafiek:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Voorbeeldrespons:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Laatste beschikbare versie uit de bovenstaande lijst is:`0.7.0-rc1`

1. Bekijk de helmdiagrammen die momenteel zijn geïnstalleerd:

    ```bash
    helm list
    ```

    Voorbeeldrespons:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    De installatie van het Helm-diagram `odd-billygoat`van de bovenstaande voorbeeldrespons wordt genoemd . We zullen deze naam gebruiken voor de rest van de commando's. Uw werkelijke implementatienaam zal waarschijnlijk verschillen.

1. Upgrade de Helm-implementatie naar een nieuwe versie:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Terugdraaien

Als de implementatie van het Roer mislukt, u terugdraaien naar een eerdere release.

1. Ontvang het laatst bekende gezonde releasenummer:

    ```bash
    helm history odd-billygoat
    ```

    Voorbeelduitvoer:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Uit de voorbeelduitvoer `helm history` van de opdracht lijkt het `odd-billygoat` erop dat de laatste succesvolle implementatie van onze revisie`1`

1. Terugdraaien naar de laatste succesvolle revisie:

    ```bash
    helm rollback odd-billygoat 1
    ```
