---
title: Rendermogelijkheden gebruiken - Azure Batch
description: Azure Batch-renderingmogelijkheden gebruiken. Probeer de Batch Explorer-toepassing rechtstreeks of aangeroepen te gebruiken via een invoegtoepassing clienttoepassing.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672014"
---
# <a name="using-azure-batch-rendering"></a>Azure Batch-rendering gebruiken

Azure Batch-rendering op verschillende manieren worden gebruikt:

* API's:
  * Schrijf code met een van de Batch API's.  Ontwikkelaars kunnen Azure Batch-mogelijkheden integreren in hun bestaande toepassingen of workflow, zowel in de cloud als op basis van on-premises.
* Opdrachtenregelgereedschappen:
  * De [Azure-opdrachtregel](https://docs.microsoft.com/cli/azure/) of [PowerShell](https://docs.microsoft.com/powershell/azure/overview) kan worden gebruikt voor het gebruik van batch.
  * Met name de [batch-CLI-sjabloonondersteuning](https://docs.microsoft.com/azure/batch/batch-cli-templates) maakt het veel gemakkelijker om pools te maken en taken in te dienen.
* Gebruikersinterface van Batch Explorer:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) is een cross-platform client tool waarmee ook Batch accounts kunnen worden beheerd en gecontroleerd.
  * Voor elk van de renderingtoepassingen worden een aantal pool- en taaksjablonen verstrekt die kunnen worden gebruikt om eenvoudig pools te maken en taken in te dienen.  Een set sjablonen wordt weergegeven in de gebruikersinterface van de toepassing, waarbij de sjabloonbestanden worden geopend vanaf GitHub.
  * Aangepaste sjablonen kunnen vanaf nul worden gemaakt of de meegeleverde sjablonen van GitHub kunnen worden gekopieerd en gewijzigd.
* Plug-ins voor clienttoepassingen:
  * Er zijn plug-ins beschikbaar waarmee batchrendering rechtstreeks binnen de clientontwerp- en modelleringstoepassingen kan worden gebruikt.  De plug-ins roepen voornamelijk de Batch Explorer-toepassing op met contextuele informatie over het huidige 3D-model en bevat functies om assets te beheren.

De beste manier om Azure Batch-rendering en eenvoudigste manier te proberen voor eindgebruikers, die geen ontwikkelaars zijn en geen Azure-experts, is door de Batch Explorer-toepassing direct te gebruiken of aante roepen vanuit een plug-in voor clienttoepassingen.

## <a name="using-batch-explorer"></a>Batch Explorer gebruiken

Zie de [zelfstudie Blender](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)voor een stapsgewijze zelfstudie voor het gebruik van Batch Explorer om rendering uit te voeren.

### <a name="download-and-install"></a>Downloaden en installeren

Batch [Explorer-downloads zijn beschikbaar](https://azure.github.io/BatchExplorer/) voor Windows, OSX en Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Sjablonen gebruiken om pools te maken en taken uit te voeren

Een uitgebreide set sjablonen is beschikbaar voor gebruik met Batch Explorer, waarmee u eenvoudig pools maken en taken hoeft in te dienen voor de verschillende renderingtoepassingen zonder dat u alle eigenschappen hoeft op te geven die nodig zijn om groepen, taken en taken rechtstreeks met Batch.  De sjablonen die beschikbaar zijn in Batch Explorer worden opgeslagen en zichtbaar in [een GitHub-opslagplaats.](https://github.com/Azure/BatchExplorer-data/tree/master/ncj)

![Galerie Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

Er worden sjablonen verstrekt die geschikt zijn voor alle toepassingen die aanwezig zijn op de Marketplace-rendering VM-afbeeldingen.  Voor elke toepassing bestaan meerdere sjablonen, waaronder poolsjablonen om tegemoet te komen aan CPU- en GPU-pools, Windows- en Linux-pools; taaksjablonen omvatten full frame of betegelde Blender-rendering en V-Ray distributed rendering. De set meegeleverde sjablonen wordt in de loop van de tijd uitgebreid om tegemoet te komen aan andere batchmogelijkheden, zoals automatische schaling voor groepen.

Het is ook mogelijk om aangepaste sjablonen te produceren, vanaf nul of door de meegeleverde sjablonen aan te passen. Aangepaste sjablonen kunnen worden gebruikt door het item 'Lokale sjablonen' te selecteren in de sectie 'Galerij' van Batch Explorer.

### <a name="file-system-and-data-movement"></a>Bestandssysteem en gegevensverplaatsing

Met de sectie 'Gegevens' in Batch Explorer kunnen bestanden worden gekopieerd tussen een lokaal bestandssysteem en Azure Storage-accounts.

## <a name="client-application-plug-ins"></a>Plug-ins voor clienttoepassingen

Plug-ins zijn beschikbaar voor sommige clienttoepassingen.  Met de plug-ins kunnen groepen en taken rechtstreeks vanuit de toepassing worden gemaakt of batchexplorer aanroepen.

* [Blender 2.79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Blender 2.8+](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Volgende stappen

Voor voorbeelden van Batch rendering probeer de twee tutorials:

* [Renderen met de Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Weergeven met Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)