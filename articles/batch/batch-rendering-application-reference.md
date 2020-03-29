---
title: Renderingtoepassingen gebruiken - Azure Batch
description: Rendering-toepassingen gebruiken met Azure Batch. In dit artikel vindt u een korte beschrijving van het uitvoeren van elke rendering-toepassing.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390487"
---
# <a name="rendering-applications"></a>Renderingtoepassingen

Renderingtoepassingen worden gebruikt door batchtaken en -taken te maken. De eigenschap taakopdrachtregel geeft de juiste opdrachtregel en parameters op.  De eenvoudigste manier om de taaktaken te maken, is door de Batch Explorer-sjablonen te gebruiken zoals gespecificeerd in [dit artikel.](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer)  De sjablonen kunnen worden bekeken en gewijzigd versies gemaakt indien nodig.

In dit artikel vindt u een korte beschrijving van het uitvoeren van elke rendering-toepassing.

## <a name="rendering-with-autodesk-3ds-max"></a>Rendering met Autodesk 3ds Max

### <a name="renderer-support"></a>Ondersteuning voor Renderer

Naast de renderers ingebouwd in 3ds Max, de volgende renderers zijn beschikbaar op de rendering VM beelden en kan worden verwezen door de 3ds Max scène bestand:

* Autodesk Arnold
* Chaos Groep V-Ray

### <a name="task-command-line"></a>Opdrachtregel voor taak

Roep `3dsmaxcmdio.exe` de toepassing aan om opdrachtregelweergave uit te voeren op een poolknooppunt.  Deze toepassing bevindt zich op het pad wanneer de taak wordt uitgevoerd. De `3dsmaxcmdio.exe` applicatie heeft dezelfde beschikbare `3dsmaxcmd.exe` parameters als de applicatie, die is gedocumenteerd in de [3ds Max help documentatie](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Rendering | Command-Line Rendering sectie).

Bijvoorbeeld:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Opmerkingen:

* Grote zorg moet worden genomen om ervoor te zorgen dat de activa bestanden worden gevonden.  Controleer of de paden correct en relatief zijn `-bitmapPath` met behulp van het venster Asset **Tracking** of gebruik de parameter op de opdrachtregel.
* Kijk of er problemen zijn met de render, zoals het `stdout.txt` onvermogen om assets te vinden, door het bestand te controleren dat is geschreven door 3ds Max wanneer de taak wordt uitgevoerd.

### <a name="batch-explorer-templates"></a>Batch Explorer-sjablonen

Pool- en taaksjablonen zijn toegankelijk vanuit de **galerie** in Batch Explorer.  De sjabloonbronbestanden zijn beschikbaar in de [batch explorer-gegevensopslagplaats op GitHub.](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax)

## <a name="rendering-with-autodesk-maya"></a>Rendering met Autodesk Maya

### <a name="renderer-support"></a>Ondersteuning voor Renderer

Naast de renderers ingebouwd in Maya, de volgende renderers zijn beschikbaar op de rendering VM beelden en kan worden verwezen door de 3ds Max scène bestand:

* Autodesk Arnold
* Chaos Groep V-Ray

### <a name="task-command-line"></a>Opdrachtregel voor taak

De `renderer.exe` opdrachtregelrenderer wordt gebruikt in de opdrachtregel van de taak. De command-line renderer is gedocumenteerd in [Maya help](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

In het volgende voorbeeld wordt een taakvoor het voorbereiden van taken gebruikt om de scènebestanden en -elementen naar de werkmap voor taakvoorbereiding te kopiëren, wordt een uitvoermap gebruikt om de renderingafbeelding op te slaan en wordt frame 10 weergegeven.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Voor V-Ray rendering, de Maya scene bestand zou normaal gesproken V-Ray als de renderer.  Het kan ook worden opgegeven op de opdrachtregel:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Voor Arnold rendering, de Maya scène bestand zou normaal gesproken Arnold als de renderer.  Het kan ook worden opgegeven op de opdrachtregel:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Batch Explorer-sjablonen

Pool- en taaksjablonen zijn toegankelijk vanuit de **galerie** in Batch Explorer.  De sjabloonbronbestanden zijn beschikbaar in de [batch explorer-gegevensopslagplaats op GitHub.](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya)

## <a name="next-steps"></a>Volgende stappen

Gebruik de pool- en taaksjablonen uit de [gegevensopslagplaats in GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) met Batch Explorer.  Maak indien nodig nieuwe sjablonen of wijzig een van de meegeleverde sjablonen.