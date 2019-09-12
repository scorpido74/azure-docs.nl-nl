---
title: Rendering van toepassingen-Azure Batch
description: Vooraf geïnstalleerde batch rendering-toepassingen
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 09/10/2019
ms.topic: conceptual
ms.openlocfilehash: 2b0a132c156cc12d317bf51488625191bb8091fc
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881465"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Vooraf geïnstalleerde toepassingen voor het renderen van VM-installatie kopieën

Het is mogelijk om alle rendering-toepassingen met Azure Batch te gebruiken. Azure Marketplace-VM-installatie kopieën zijn echter beschikbaar met vooraf geïnstalleerde toepassingen.

Indien van toepassing is licenties voor betalen per gebruik beschikbaar voor de vooraf geïnstalleerde rendering-toepassingen. Wanneer een batch-pool wordt gemaakt, kunnen de vereiste toepassingen worden opgegeven en worden de kosten van de VM en de toepassingen per minuut in rekening gebracht. Toepassings prijzen worden vermeld op de [pagina met Azure batch prijzen](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Sommige toepassingen bieden alleen ondersteuning voor Windows, maar de meeste worden ondersteund voor zowel Windows als Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Toepassingen op CentOS 7-rendering van installatie kopieën

De volgende lijst is van toepassing op CentOS 7,6, version 1.1.6 rendering installatie kopieën.

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018-update 2 (knippen 201711281015)
* Update van I/O 2019 Autodesk Maya 1
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold voor Maya 2019 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (versie 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versie 3.60.04)
* Blender (2.68)
* Blender (2,8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Toepassingen op de nieuwste installatie kopieën van Windows Server 2016-Rendering

De volgende lijst is van toepassing op Windows Server 2016, version 1.3.7 rendering installatie kopieën.

* Autodesk Maya I/O 2017 Update 5 (versie 17.4.5459)
* Autodesk Maya I/O 2018 update 4 (versie 18.4.0.7622)
* Auto Desk 3ds Max I/O 2019 update 1 (versie 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versie 20.4.0.4254)
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold voor 3ds Max 2018 (Arnold version 5.0.2.4) (versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2019 (Arnold version 5.0.2.4) (versie 1.2.926)
* Chaos-groep V-Ray voor Maya 2018 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2018 (versie 3.60.02)
* Chaos-groep V-Ray voor Maya 2019 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2019 (versie 4.10.01)
* Blender (2.79)


> [!NOTE]
> Chaos Group V-Ray voor 3ds Max 2019 (versie 4.10.01) introduceert een belang rijke wijziging in de V-Ray. Als u de vorige versie (versie 3.60.02) wilt gebruiken, gebruikt u Windows Server 2016, versie 1.3.2 rendering-knoop punten.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Toepassingen op eerdere Windows Server 2016-rendering van installatie kopieën

De volgende lijst is van toepassing op Windows Server 2016, version 1.3.2 rendering installatie kopieën.

* Autodesk Maya I/O 2017 Update 5 (versie 17.4.5459)
* Autodesk Maya I/O 2018 update 4 (versie 18.4.0.7622)  
* Auto Desk 3ds Max I/O 2019 update 1 (versie 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versie 20.4.0.4254)
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold voor 3ds Max (Arnold version 5.0.2.4) (versie 1.2.926)
* Chaos-groep V-Ray voor Maya 2019 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2018 (versie 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Volgende stappen

Als u de weer gave-VM-installatie kopieën wilt gebruiken, moet u deze opgeven in de groeps configuratie wanneer er een groep wordt gemaakt. Bekijk de [mogelijkheden van de batch-pool voor rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
