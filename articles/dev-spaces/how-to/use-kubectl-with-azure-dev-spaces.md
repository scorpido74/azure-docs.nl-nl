---
title: Kubectl gebruiken met Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van kubectl-opdrachten binnen een dev-ruimte op een Azure Kubernetes service-cluster waarvoor Azure dev Spaces is ingeschakeld
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: 7530cde68b2ce8d06fb7b16a9a147f0f0bc8645c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75438365"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Kubectl gebruiken met een Azure dev Space

U hebt toegang tot het Kubernetes-cluster binnen een Azure-ontwikkel ruimte en u kunt bestaande `kubectl`Kubernetes-hulpprogram ma's gebruiken, zoals.

Als `az aks use-dev-spaces` u de opdracht uitvoert, `kubectl` wordt er automatisch een configuratie context toegevoegd, dus kubectl moet al zijn verbonden met uw Azure dev Spaces Kubernetes-cluster. Voorbeelden:
- De huidige context bevestigen:`kubectl config current-context`
- Alle beschik bare contexten `kubectl config get-contexts`weer geven:. 
- Context wijzigen:`kubectl config use-context <context-name>`
- Bekijk het Kubernetes-dash board `kubectl proxy`: Voer uit en open vervolgens uw browser op het adres dat met deze `/ui` opdracht wordt meegegeven (Voeg toe aan de URL om naar het Kubernetes-dash board te gaan).
- Geef de actieve services weer in de standaard Space van Azure dev Spaces met de naam *default*:`kubectl get services --namespace=default`

