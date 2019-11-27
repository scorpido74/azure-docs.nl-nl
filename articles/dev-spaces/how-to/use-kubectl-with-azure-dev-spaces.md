---
title: Kubectl gebruiken met Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: fea14668c35eddcd01417b417e2239788eddd6d1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279932"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Gebruik kubectl met een Azure Dev Space

U hebt toegang tot het Kubernetes-cluster binnen een Azure-ontwikkel ruimte en u kunt bestaande Kubernetes-hulpprogram ma's gebruiken, zoals `kubectl`.

Als `az aks use-dev-spaces` opdracht wordt uitgevoerd, wordt automatisch een `kubectl` configuratie context toegevoegd voor u, dus kubectl moet al zijn verbonden met uw Azure dev Spaces Kubernetes-cluster. Voorbeelden:
- De huidige context bevestigen: `kubectl config current-context`
- Lijst met alle beschik bare contexten: `kubectl config get-contexts`. 
- Context wijzigen: `kubectl config use-context <context-name>`
- Bekijk het Kubernetes-dash board: Voer `kubectl proxy`uit en open de browser op het adres dat met deze opdracht wordt meegegeven (Voeg `/ui` toe aan de URL om naar het Kubernetes-dash board te navigeren).
- Geef de actieve services weer in de standaard Space van Azure dev Spaces met de naam *standaard*: `kubectl get services --namespace=default`

