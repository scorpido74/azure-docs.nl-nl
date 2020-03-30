---
title: Ondersteunde resources voor Azure Red Hat OpenShift
description: Inzicht in welke Azure-regio's en virtuele machineformaten worden ondersteund door Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243664"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift-bronnen

In dit onderwerp worden de Azure-regio's en virtuele machineformaten weergegeven die worden ondersteund door de Microsoft Azure Red Hat OpenShift-service.

## <a name="azure-regions"></a>Azure-regio's

Zie [Producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) voor een huidige lijst met regio's waar u Azure Red Hat OpenShift-clusters implementeren.

## <a name="virtual-machine-sizes"></a>Grootten van virtuele machines

Dit zijn de ondersteunde virtuele machineformaten die u opgeven voor de compute nodes in uw Azure Red Hat OpenShift-cluster.

> [!Important]
> Elke VM heeft een ander aantal stations die kunnen worden aangesloten. Dit is misschien niet zo onmiddellijk duidelijk als geheugen of CPU-grootte.
> Niet alle VM-formaten zijn beschikbaar in alle regio's. Zelfs als de API de opgegeven grootte ondersteunt, krijgt u mogelijk een foutmelding als de grootte niet beschikbaar is in de regio die u opgeeft.
> Zie [Huidige lijst met ondersteunde VM-formaten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor meer informatie.

## <a name="compute-node-sizes"></a>Grootte van rekenknooppunt

De volgende compute node-grootten worden ondersteund door de Azure Red Hat OpenShift REST API:

|Grootte|vCPU|RAM|
|-|-|-|
|Standaard D4s v3|4|16 GB|
|Standaard D8s v3|8|32 GB|
|Standaard D16s v3|16|64 GB|
|Standaard D32s v3|32|128 GB|
|-|-|-|
|Standaard E4s v3|4|32 GB|
|Standaard E8s v3|8|64 GB|
|Standaard E16s v3|16|128 GB|
|Standaard E32s v3|32|256 GB|
|-|-|-|
|Standaard F8s v2|8|16 GB|
|Standaard F16s v2|16|32 GB|
|Standaard F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>Grootte van het hoofdknooppunt

De volgende hoofd-/infrastructuurknooppuntgroottes worden ondersteund door de Azure Red Hat OpenShift REST API:

|Grootte|vCPU|RAM|
|-|-|-|
|Standaard D4s v3|4|16 GB|
|Standaard D8s v3|8|32 GB|
|Standaard D16s v3|16|64 GB|
|Standaard D32s v3|32|128 GB|

## <a name="next-steps"></a>Volgende stappen

Probeer de [zelfstudie van Een Azure Red Hat OpenShift-cluster maken.](tutorial-create-cluster.md)
