---
title: Kubectl gebruiken met Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van kubectl-opdrachten binnen een dev-ruimte op een Azure Kubernetes Service-cluster met Azure Dev Spaces ingeschakeld
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438365"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl gebruiken met een Azure Dev Space

U hebt toegang tot het Kubernetes-cluster in een Azure `kubectl`Dev Space en bestaande Kubernetes-hulpprogramma's gebruiken, zoals.

Als `az aks use-dev-spaces` u de opdracht `kubectl` uitvoert, wordt automatisch een configuratiecontext voor u toegevoegd, zodat kubectl al moet zijn verbonden met uw Azure Dev Spaces Kubernetes-cluster. Voorbeelden:
- Bevestig de huidige context:`kubectl config current-context`
- Vermeld alle beschikbare `kubectl config get-contexts`contexten: . 
- Context wijzigen:`kubectl config use-context <context-name>`
- Bekijk het Kubernetes-dashboard: voer uit `kubectl proxy`en open vervolgens uw browser `/ui` naar het adres dat deze opdracht uitzendt (voeg toe aan de URL om naar het Kubernetes-dashboard te navigeren).
- De uitvoerende services weergeven in de standaardruimte Azure Dev Spaces met de naam *standaard:*`kubectl get services --namespace=default`

