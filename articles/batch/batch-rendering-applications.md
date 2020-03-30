---
title: Renderingtoepassingen - Azure Batch
description: Het is mogelijk om renderingtoepassingen te gebruiken met Azure Batch. Azure Marketplace VM-afbeeldingen zijn echter beschikbaar met algemene toepassingen die vooraf zijn geïnstalleerd.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022984"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Vooraf geïnstalleerde toepassingen voor het renderen van VM-afbeeldingen

Het is mogelijk om renderingtoepassingen te gebruiken met Azure Batch. Azure Marketplace VM-afbeeldingen zijn echter beschikbaar met algemene toepassingen die vooraf zijn geïnstalleerd.

Waar van toepassing is pay-per-use licenties beschikbaar voor de vooraf geïnstalleerde renderingtoepassingen. Wanneer een batchgroep wordt gemaakt, kunnen de vereiste toepassingen worden opgegeven en worden zowel de kosten van VM als toepassingen per minuut gefactureerd. Toepassingsprijzen worden vermeld op de [prijspagina van Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Sommige toepassingen ondersteunen alleen Windows, maar de meeste worden ondersteund op zowel Windows als Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Toepassingen op CentOS 7 rendering beelden

De volgende lijst is van toepassing op CentOS 7.6, versie 1.1.6 rendering images.

* Autodesk Maya I/O 2017 Update 5 (cut 201708032230)
* Autodesk Maya I/O 2018 Update 2 (cut 201711281015)
* Autodesk Maya I/O 2019 Update 1
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold voor Maya 2019 (Arnold versie 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (versie 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versie 3.60.04)
* Blender (2.68)
* Blender (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Toepassingen op de nieuwste weergaveafbeeldingen van Windows Server 2016

De volgende lijst is van toepassing op Windows Server 2016, weergaveafbeeldingen van versie 1.3.8.

* Autodesk Maya I/O 2017 Update 5 (versie 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (versie 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Update 4 (versie 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (versie 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold voor Maya 2019 (Arnold versie 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold voor 3ds Max 2018 (Arnold versie 5.3.0.2)(versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2019 (Arnold versie 5.3.0.2)(versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2020 (Arnold versie 5.3.0.2)(versie 1.2.926)
* Chaos Group V-Ray voor Maya 2017 (versie 4.12.01)
* Chaos Group V-Ray voor Maya 2018 (versie 4.12.01)
* Chaos Group V-Ray voor Maya 2019 (versie 4.04.03)
* Chaos Group V-Ray voor 3ds Max 2018 (versie 4.20.01)
* Chaos Group V-Ray voor 3ds Max 2019 (versie 4.20.01)
* Chaos Group V-Ray voor 3ds Max 2020 (versie 4.20.01)
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> Als u V-Ray met Maya buiten de `vrayses.exe` [Azure Batch-extensiesjablonen](https://github.com/Azure/batch-extension-templates)wilt uitvoeren, begint u voordat u de render uitvoert. Als u de vrayses.exe buiten de sjablonen `%MAYA_2017%\vray\bin\vrayses.exe"`wilt starten, u de volgende opdracht gebruiken.
>
> Zie bijvoorbeeld de starttaak van de [Maya- en V-Ray-sjabloon](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) op GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Toepassingen op eerdere weergaveafbeeldingen van Windows Server 2016

De volgende lijst is van toepassing op Windows Server 2016, weergaveafbeeldingen van versie 1.3.7.

* Autodesk Maya I/O 2017 Update 5 (versie 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versie 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (versie 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Update 4 (versie 20.4.0.4254)
* Autodesk Arnold voor Maya 2017 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold voor Maya 2018 (Arnold versie 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold voor 3ds Max 2018 (Arnold versie 5.0.2.4)(versie 1.2.926)
* Autodesk Arnold voor 3ds Max 2019 (Arnold versie 5.0.2.4)(versie 1.2.926)
* Chaos Group V-Ray voor Maya 2018 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2018 (versie 3.60.02)
* Chaos Group V-Ray voor Maya 2019 (versie 3.52.03)
* Chaos Group V-Ray voor 3ds Max 2019 (versie 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray voor 3ds Max 2019 (versie 4.10.01) introduceert baanbrekende wijzigingen in V-ray. Als u de vorige versie (versie 3.60.02) wilt gebruiken, gebruikt u Windows Server 2016, renderingnodes van versie 1.3.2.

## <a name="next-steps"></a>Volgende stappen

Als u de rendering VM-afbeeldingen wilt gebruiken, moeten ze worden opgegeven in de poolconfiguratie wanneer een groep wordt gemaakt. zie de [mogelijkheden van de batchgroep voor rendering](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
