---
title: Renderingmogelijkheden - Azure Batch
description: Standaard Azure Batch-mogelijkheden worden gebruikt om renderingworkloads en -apps uit te voeren. Batch bevat specifieke functies ter ondersteuning van rendering workloads.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 697e2640b7215e0bbb9202c672f936535831eb99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449714"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch-renderingmogelijkheden

Standaard Azure Batch-mogelijkheden worden gebruikt om renderingworkloads en -toepassingen uit te voeren. Batch bevat ook specifieke functies ter ondersteuning van rendering workloads.

Zie [dit artikel](https://docs.microsoft.com/azure/batch/batch-api-basics)voor een overzicht van batchconcepten, waaronder pools, taken en taken.

## <a name="batch-pools"></a>Batchpools

### <a name="rendering-application-installation"></a>Installatie van rendertoepassingen

Een AZURE Marketplace-rendering VM-afbeelding kan worden opgegeven in de poolconfiguratie als alleen de vooraf geïnstalleerde toepassingen moeten worden gebruikt.

Er is een Windows 2016-afbeelding en een CentOS-afbeelding.  In de [Azure Marketplace](https://azuremarketplace.microsoft.com)zijn de VM-afbeeldingen te vinden door te zoeken naar 'batch rendering'.

Zie de [zelfstudie Azure CLI-rendering](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)voor een voorbeeldpoolconfiguratie .  De Azure-portal en Batch Explorer bieden GUI-hulpprogramma's om een rendering VM-afbeelding te selecteren wanneer u een groep maakt.  Als u een Batch-API gebruikt, geeft u bij het maken van een groep de volgende eigenschapswaarden op voor [ImageReference:](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference)

| Uitgever | Aanbieding | Sku | Versie |
|---------|---------|---------|--------|
| batch | rendering-centos73 | rendering | meest recente |
| batch | rendering-windows2016 | rendering | meest recente |

Andere opties zijn beschikbaar als aanvullende toepassingen nodig zijn op de pool VM's:

* Een aangepaste afbeelding uit de gedeelde afbeeldingsgalerie:
  * Met deze optie kunt u uw virtuele machine configureren met de exacte toepassingen en specifieke versies die u nodig hebt. Zie [Een pool maken met de galerie Voor gedeelde afbeeldingen](batch-sig-images.md)voor meer informatie. Autodesk en Chaos Group hebben Arnold en V-Ray gewijzigd om te valideren tegen een Azure Batch-licentieservice. Zorg ervoor dat u de versies van deze toepassingen met deze ondersteuning, anders de pay-per-use licenties zal niet werken. Huidige versies van Maya of 3ds Max vereisen geen licentieserver wanneer u headless draait (in batch/command-line-modus). Neem contact op met Azure-ondersteuning als u niet zeker weet hoe u met deze optie moet omgaan.
* [Aanvraagpakketten](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Verpak de toepassingsbestanden met een of meer ZIP-bestanden, upload via de Azure-portal en geef het pakket op in de configuratie van de groep. Wanneer pool VM's worden gemaakt, worden de ZIP-bestanden gedownload en worden de bestanden geëxtraheerd.
* Bronbestanden:
  * Toepassingsbestanden worden geüpload naar Azure blob-opslag en u geeft bestandsverwijzingen op in de [groepstarttaak.](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) Wanneer er chatvm's worden gemaakt, worden de bronbestanden gedownload naar elke vm.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licenties voor betalen voor gebruik voor vooraf geïnstalleerde toepassingen

De toepassingen die worden gebruikt en licentiekosten hebben, moeten worden opgegeven in de poolconfiguratie.

* Geef `applicationLicenses` de eigenschap op bij [het maken van een pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  De volgende waarden kunnen worden opgegeven in de array van tekenreeksen - "vray", "arnold", "3dsmax", "maya".
* Wanneer u een of meer toepassingen opgeeft, worden de kosten van deze toepassingen toegevoegd aan de kosten van de VM's.  Toepassingsprijzen worden vermeld op de [prijspagina van Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Als u in plaats daarvan verbinding maakt met een `applicationLicenses` licentieserver om de renderingtoepassingen te gebruiken, geeft u de eigenschap niet op.

U de Azure-portal of Batch Explorer gebruiken om toepassingen te selecteren en de toepassingsprijzen weer te geven.

Als er een poging wordt gedaan om een toepassing te `applicationLicenses` gebruiken, maar de toepassing niet is opgegeven in de eigenschap van de poolconfiguratie of geen licentieserver bereikt, mislukt de uitvoering van de toepassing met een licentiefout en niet-nulexitcode.

### <a name="environment-variables-for-pre-installed-applications"></a>Omgevingsvariabelen voor vooraf geïnstalleerde toepassingen

Om de opdrachtregel voor renderingtaken te kunnen maken, moet de installatielocatie van de rendertoepassingsuitvoerbare bestanden worden opgegeven.  Systeemomgevingsvariabelen zijn gemaakt op de Azure Marketplace VM-afbeeldingen, die kunnen worden gebruikt in plaats van werkelijke paden op te geven.  Deze omgevingsvariabelen vormen een aanvulling op de [standaard batchomgevingsvariabelen](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) die voor elke taak zijn gemaakt.

|Toepassing|Toepassings uitvoerbaar|Omgevingsvariabele|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Groep V-Ray Standalone|vray.exe|VRAY_3.60.4_EXEC|
Opdrachtregel Arnold 2017|kick.exe kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 opdrachtregel|kick.exe kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure VM-families

Net als bij andere workloads variëren de vereisten voor het rendersysteem voor toepassingen en de prestatievereisten voor taken en projecten.  Een grote verscheidenheid aan VM-families zijn beschikbaar in Azure, afhankelijk van uw vereisten: laagste kosten, de beste prijs/prestatie, de beste prestaties, enzovoort.
Sommige rendering toepassingen, zoals Arnold, zijn CPU-gebaseerde; andere zoals V-Ray en Blender Cycles kunnen CPU's en/of GPU's gebruiken.
[Zie VM-typen en -formaten voor](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)een beschrijving van beschikbare VM-families en VM-formaten.

### <a name="low-priority-vms"></a>Virtuele machines met lage prioriteit

Net als bij andere workloads kunnen VM's met lage prioriteit worden gebruikt in batchgroepen voor rendering.  Vm's met lage prioriteit presteren hetzelfde als gewone speciale VM's, maar maken gebruik van een extra Azure-capaciteit en zijn beschikbaar voor een grote korting.  De afweging voor het gebruik van VM's met lage prioriteit is dat deze VM's mogelijk niet beschikbaar zijn om te worden toegewezen of op elk moment kunnen worden uitgesloten, afhankelijk van de beschikbare capaciteit. Om deze reden zijn VM's met lage prioriteit niet geschikt voor alle rendering-taken. Als afbeeldingen bijvoorbeeld vele uren duren om te renderen, is het waarschijnlijk dat het niet acceptabel is dat de weergave van die afbeeldingen wordt onderbroken en opnieuw wordt gestart omdat VM's worden voorgekomen.

Zie [VM's met lage prioriteit gebruiken met batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms)voor meer informatie over de kenmerken van VM's met lage prioriteit en de verschillende manieren om deze te configureren met batch.

## <a name="jobs-and-tasks"></a>Taken en taken

Er is geen rendering-specifieke ondersteuning vereist voor taken en taken.  Het belangrijkste configuratie-item is de taakopdrachtregel, die moet verwijzen naar de vereiste toepassing.
Wanneer de Azure Marketplace VM-afbeeldingen worden gebruikt, u de omgevingsvariabelen het beste gebruiken om het pad en de uitvoerbare toepassing op te geven.

## <a name="next-steps"></a>Volgende stappen

Voor voorbeelden van Batch rendering probeer de twee tutorials:

* [Renderen met de Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Weergeven met Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
