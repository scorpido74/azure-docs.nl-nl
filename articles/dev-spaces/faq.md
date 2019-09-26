---
title: Veelgestelde vragen over Azure dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Antwoorden vinden op enkele veelgestelde vragen over Azure dev Spaces
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: de44bf323b300a4467dc8b30555798f557898494
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71305975"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Veelgestelde vragen over Azure dev Spaces

Dit is een oplossing voor veelgestelde vragen over Azure dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Welke Azure-regio's bevatten momenteel Azure dev Spaces?

Zie [ondersteunde regio's en configuraties][supported-regions]voor een volledige lijst met beschik bare regio's.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kan ik Azure dev Spaces gebruiken zonder een openbaar IP-adres?

Nee, u kunt geen Azure dev-ruimten inrichten op een AKS-cluster zonder een openbaar IP-adres. Er is een openbaar IP-adres [nodig door Azure dev Spaces voor route ring][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kan ik mijn eigen ingangen gebruiken met Azure dev Spaces?

Ja, u kunt uw eigen inkomend verkeer configureren aan de zijde die door Azure dev Spaces wordt gemaakt. U kunt bijvoorbeeld [traefik][ingress-traefik]gebruiken.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kan ik HTTPS gebruiken met Azure dev Spaces?

Ja, u kunt uw eigen inkomend verkeer configureren met HTTPS met behulp van [traefik][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kan ik Azure dev Spaces gebruiken in een cluster dat gebruikmaakt van CNI in plaats van kubenet? 

Ja, u kunt Azure dev Spaces gebruiken in een AKS-cluster dat gebruikmaakt van CNI voor netwerken. U kunt bijvoorbeeld Azure dev Spaces gebruiken in een AKS-cluster met [bestaande Windows-containers][windows-containers], die gebruikmaken van cni voor netwerken.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kan ik Azure dev Spaces gebruiken met Windows-containers?

Momenteel is Azure dev Spaces alleen bedoeld om te worden uitgevoerd op Linux en alleen knoop punten, maar u kunt Azure dev-ruimten uitvoeren op een AKS-cluster met [bestaande Windows-containers][windows-containers].


[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md