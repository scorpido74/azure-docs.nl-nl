---
title: Ondersteunde bronnen voor Azure Red Hat open Shift
description: Begrijp welke Azure-regio's en virtuele-machine grootten worden ondersteund door Microsoft Azure Red Hat open SHIFT.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381444"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat open Shift-resources

Dit onderwerp bevat een overzicht van de Azure-regio's en de grootte van virtuele machines die worden ondersteund door de Microsoft Azure Red Hat open Shift service.

## <a name="azure-regions"></a>Azure-regio's

Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) voor een actuele lijst met regio's waarin u Azure Red Hat open Shift-clusters kunt implementeren.

## <a name="virtual-machine-sizes"></a>Grootten van virtuele machines

Hier vindt u de ondersteunde grootten voor virtuele machines die u kunt opgeven voor de reken knooppunten in uw Azure Red Hat open Shift-cluster.

> [!Important]
> Elke virtuele machine heeft een verschillend aantal stations dat kan worden bijgevoegd. Dit is mogelijk niet zo onmiddellijk als geheugen of CPU-grootte.
> Niet alle VM-grootten zijn in alle regio's beschikbaar. Zelfs als de API de door u opgegeven grootte ondersteunt, krijgt u mogelijk een fout melding als de grootte niet beschikbaar is in de regio die u opgeeft.
> Bekijk de [huidige lijst met ondersteunde VM-grootten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor meer informatie.

## <a name="compute-node-sizes"></a>Grootte van reken knooppunten

De volgende grootte van reken knooppunten worden ondersteund door de Azure Red Hat open Shift REST API:

|Grootte|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|
|-|-|-|
|Standard E4s v3|4|32 GB|
|Standard E8s v3|8|64 GB|
|Standard E16s v3|16|128 GB|
|Standard E32s v3|32|256 GB|
|-|-|-|
|Standard F8s versie 2|8|16 GB|
|Standard F16s versie 2|16|32 GB|
|Standard F32s versie 2|32|64 GB|

## <a name="master-node-sizes"></a>Grootte van hoofd knooppunt

De volgende hoofd-en infrastructuur knooppunt grootten worden ondersteund door de Azure Red Hat open Shift REST API:

|Grootte|vCPU|RAM|
|-|-|-|
|Standard D4s v3|4|16 GB|
|Standard D8s v3|8|32 GB|
|Standard D16s v3|16|64 GB|
|Standard D32s v3|32|128 GB|

## <a name="next-steps"></a>Volgende stappen

Probeer de zelf studie [een Azure Red Hat open Shift-cluster maken](tutorial-create-cluster.md) .
