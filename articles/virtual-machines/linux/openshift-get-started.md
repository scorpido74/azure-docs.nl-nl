---
title: OpenShift in Azure-overzicht
description: Een overzicht van OpenShift in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c338e9a6a793d1c2d0557d70242996175d5a85ed
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759451"
---
# <a name="openshift-in-azure"></a>OpenShift in Azure

OpenShift is een open en uitbreidbaar containerapplicatieplatform dat Docker en Kubernetes naar de onderneming brengt.  

OpenShift bevat Kubernetes voor containerorchestration en beheer. Het voegt op ontwikkelaars gerichte en operations-centric tools toe die:

- Snelle applicatieontwikkeling.
- Eenvoudige implementatie en schaling.
- Onderhoud van de levenscyclus op lange termijn voor teams en toepassingen.

Er zijn meerdere versies van OpenShift beschikbaar.  Van deze versies zijn er vandaag de dag slechts twee beschikbaar voor klanten die kunnen worden geïmplementeerd in Azure: OpenShift Container Platform en OKD (voorheen OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift is een volledig beheerd aanbod van OpenShift dat in Azure wordt uitgevoerd. Deze service wordt gezamenlijk beheerd en ondersteund door Microsoft en Red Hat. Zie de Azure [Red Hat OpenShift-servicedocumentatie](https://docs.microsoft.com/azure/openshift/) voor meer informatie.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform is een bedrijfsklare [commerciële versie](https://www.openshift.com) van en ondersteund door Red Hat. Met deze versie kopen klanten de benodigde rechten voor OpenShift Container Platform en zijn ze verantwoordelijk voor de installatie en het beheer van de gehele infrastructuur.

Omdat klanten het hele platform "bezitten", kunnen ze het installeren in hun on-premises datacenter of in een openbare cloud (zoals Azure).

## <a name="okd"></a>OKD

OKD is een [open-source](https://www.okd.io/) upstream project van OpenShift dat de gemeenschap ondersteund. OKD kan worden geïnstalleerd op CentOS of Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Volgende stappen

- [Algemene vereisten configureren voor OpenShift in Azure](./openshift-container-platform-3x-prerequisites.md)
- [OpenShift-containerplatform implementeren in Azure](./openshift-container-platform-3x.md)
- [OpenShift Container Platform Self-Managed Marketplace-aanbieding implementeren](./openshift-container-platform-3x-marketplace-self-managed.md)
- [OpenShift implementeren in Azure Stack](./openshift-azure-stack.md)
- [Taken na implementatie](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift-implementatie oplossen](./openshift-container-platform-3x-troubleshooting.md)
