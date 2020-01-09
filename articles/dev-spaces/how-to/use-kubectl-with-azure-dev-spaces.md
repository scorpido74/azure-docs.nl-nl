---
title: Kubectl gebruiken met Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van kubectl-opdrachten binnen een dev-ruimte op een Azure Kubernetes service-cluster waarvoor Azure dev Spaces is ingeschakeld
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438365"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Gebruik kubectl met een Azure Dev Space

U kunt toegang krijgen tot het Kubernetes-cluster in een Azure Dev Space, en gebruik maken van bestaande Kubernetes-hulpprogramma's zoals `kubectl`.

Met de opdracht `az aks use-dev-spaces` wordt automatisch een `kubectl` configuratiecontext voor u toegevoegd, zodat kubectl al zal zijn verbonden met uw Azure Dev Spaces Kubernetes-cluster. Voorbeelden:
- Controleer de huidige context: `kubectl config current-context`
- Toon een lijst met alle beschikbare contexten: `kubectl config get-contexts`. 
- Wijzig de context: `kubectl config use-context <context-name>`
- Bekijk het Kubernetes dashboard: voer uit `kubectl proxy`, open uw browser naar het adres dat deze opdracht weergeeft (voeg `/ui` toe aan de URL om naar het Kubernetes-dashboard te navigeren).
- Toon een lijst met de actieve services in de standaard Azure Dev Spaces ruimte met de naam *default*: `kubectl get services --namespace=default`

