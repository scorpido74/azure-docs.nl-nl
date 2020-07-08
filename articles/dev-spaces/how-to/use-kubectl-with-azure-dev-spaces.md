---
title: Kubectl gebruiken met Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van kubectl-opdrachten binnen een dev-ruimte op een Azure Kubernetes service-cluster waarvoor Azure dev Spaces is ingeschakeld
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75438365"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl gebruiken met een Azure dev Space

U hebt toegang tot het Kubernetes-cluster binnen een Azure-ontwikkel ruimte en u kunt bestaande Kubernetes-hulpprogram ma's gebruiken, zoals `kubectl` .

`az aks use-dev-spaces`Als u de opdracht uitvoert, wordt er automatisch een `kubectl` configuratie context toegevoegd, dus kubectl moet al zijn verbonden met uw Azure dev Spaces Kubernetes-cluster. Voorbeelden:
- De huidige context bevestigen:`kubectl config current-context`
- Alle beschik bare contexten weer geven: `kubectl config get-contexts` . 
- Context wijzigen:`kubectl config use-context <context-name>`
- Bekijk het Kubernetes-dash board: Voer uit `kubectl proxy` en open vervolgens uw browser op het adres dat met deze opdracht wordt meegegeven (Voeg `/ui` toe aan de URL om naar het Kubernetes-dash board te gaan).
- Geef de actieve services weer in de standaard Space van Azure dev Spaces met de naam *default*:`kubectl get services --namespace=default`

