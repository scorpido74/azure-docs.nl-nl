---
title: Weergave mogelijkheden gebruiken-Azure Batch
description: Azure Batch weergave mogelijkheden gebruiken. Gebruik de Batch Explorer toepassing, hetzij rechtstreeks of aangeroepen vanuit een invoeg toepassing voor een client-app.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672014"
---
# <a name="using-azure-batch-rendering"></a>Azure Batch Rendering gebruiken

Er zijn verschillende manieren om Azure Batch rendering te gebruiken:

* APIs
  * Code schrijven met behulp van een van de batch-Api's.  Ontwikkel aars kunnen Azure Batch-mogelijkheden integreren in hun bestaande toepassingen of werk stroom, of deze in de Cloud of op basis van on-premises.
* Opdracht regel Programma's:
  * De [Azure-opdracht regel](https://docs.microsoft.com/cli/azure/) of [Power shell](https://docs.microsoft.com/powershell/azure/overview) kan worden gebruikt om batch gebruik te maken van scripts.
  * Met name de [batch-cli-sjabloon ondersteuning](https://docs.microsoft.com/azure/batch/batch-cli-templates) maakt het veel eenvoudiger om Pools te maken en taken te verzenden.
* Batch Explorer-gebruikers interface:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) is een platformoverschrijdende client hulpprogramma waarmee batch-accounts ook kunnen worden beheerd en bewaakt.
  * Voor elk van de rendering-toepassingen worden een aantal groeps-en taak sjablonen gegeven die kunnen worden gebruikt om eenvoudig Pools te maken en taken te verzenden.  Een set sjablonen wordt weer gegeven in de gebruikers interface van de toepassing, waarbij de sjabloon bestanden worden geopend vanuit GitHub.
  * Aangepaste sjablonen kunnen helemaal zelf worden gemaakt of de opgegeven sjablonen van GitHub kunnen worden gekopieerd en gewijzigd.
* Invoeg toepassingen voor client toepassingen:
  * Invoeg toepassingen zijn beschikbaar waarmee batch rendering rechtstreeks kan worden gebruikt in de client ontwerp-en modelleer toepassingen.  De invoeg toepassingen roepen voornamelijk de Batch Explorer-toepassing met contextuele informatie over het huidige 3D-model en bevatten functies waarmee u assets kunt beheren.

De beste manier om Azure Batch rendering en de eenvoudigste manier te proberen voor eind gebruikers, die geen ontwikkel aars zijn en geen Azure-experts zijn, is het gebruik van de Batch Explorer toepassing, hetzij rechtstreeks, hetzij opgeroepen vanuit een invoeg toepassing voor client toepassingen.

## <a name="using-batch-explorer"></a>Batch Explorer gebruiken

Zie voor een stapsgewijze zelf studie voor het gebruik van Batch Explorer om weer gave uit te voeren de [zelf studie over mengsels](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Downloaden en installeren

Batch Explorer- [down loads zijn beschikbaar](https://azure.github.io/BatchExplorer/) voor Windows, OSX en Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Sjablonen gebruiken om Pools te maken en taken uit te voeren

Er is een uitgebreide set sjablonen beschikbaar voor gebruik met Batch Explorer waarmee u gemakkelijk Pools kunt maken en taken voor de verschillende rendering-toepassingen kunt verzenden, zonder dat u alle eigenschappen hoeft op te geven die nodig zijn om groepen, taken en taken rechtstreeks te maken met Meerdere.  De sjablonen die beschikbaar zijn in Batch Explorer, worden opgeslagen en weer gegeven in [een github-opslag plaats](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galerie Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

Er zijn sjablonen beschikbaar voor alle toepassingen die aanwezig zijn op de Marketplace VM-installatie kopieën weer geven.  Voor elke toepassing bestaan er meerdere sjablonen, met inbegrip van groeps sjablonen die moeten worden gebruikt voor CPU-en GPU-groepen, Windows-en Linux-Pools; taak sjablonen omvatten volledige kaders of tegel beeldrendering en V-Ray-gedistribueerde rendering. De set geleverde sjablonen wordt na verloop van tijd uitgebreid naar andere batch-mogelijkheden, zoals het automatisch schalen van groepen.

Het is ook mogelijk om aangepaste sjablonen te produceren, helemaal volledig te maken of door de opgegeven sjablonen te wijzigen. Aangepaste sjablonen kunnen worden gebruikt door het item ' lokale sjablonen ' te selecteren in de sectie ' galerie ' van Batch Explorer.

### <a name="file-system-and-data-movement"></a>Bestands systeem en gegevens verplaatsing

Met de sectie ' gegevens ' in Batch Explorer kunnen bestanden worden gekopieerd tussen een lokaal bestands systeem en Azure Storage-accounts.

## <a name="client-application-plug-ins"></a>Invoeg toepassingen voor client toepassingen

Invoeg toepassingen zijn beschikbaar voor sommige client toepassingen.  Met de invoeg toepassingen kunnen Pools en taken rechtstreeks vanuit de toepassing worden gemaakt of Batch Explorer worden aangeroepen.

* [Blender 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Blender 2.8 +](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Auto Desk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Volgende stappen

Voor voor beelden van batch rendering kunt u de twee zelf studies uitproberen:

* [Rendering met behulp van Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Rendering met Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)