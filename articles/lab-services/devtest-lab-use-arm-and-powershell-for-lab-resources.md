---
title: Labs maken of wijzigen met Azure Resource Manager-sjablonen
description: Meer informatie over het automatisch maken of wijzigen van Azure Resource Manager-sjablonen met PowerShell om automatisch labs te maken of te wijzigen in een DevTest-lab
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 180081829387afeb7e63defe7a22378aa8d417d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170296"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Labs automatisch maken of wijzigen met Azure Resource Manager-sjablonen en PowerShell

DevTest Labs biedt veel Azure Resource Manager-sjablonen en PowerShell-scripts waarmee u snel en automatisch nieuwe labs maken of bestaande labs wijzigen en deze bronnen vervolgens implementeren.

Dit artikel helpt u bij het gebruik van deze sjablonen en scripts om het maken, wijzigen en implementeren van uw labs te automatiseren. In dit artikel ziet u ook waar u meer informatie vinden over het gebruik van PowerShell om enkele veelvoorkomende taken uit te voeren in DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Stap 1: Uw sjablonen en scripts verzamelen
U vooraf gemaakte [Azure Resource Manager-sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) en [PowerShell-scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) vinden in onze openbare [GitHub-repository.](https://github.com/Azure/azure-devtestlab) Gebruik ze zoals-is, of pas ze aan voor uw behoeften en bewaar ze in uw eigen [prive-Git repo](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Stap 2: Uw azure resource manager-sjabloon wijzigen
U de stappen volgen bij [Uw eerste Azure Resource Manager-sjabloon maken](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template) als u nog nooit een sjabloon hebt gemaakt.

Daarnaast bieden [aanbevolen procedures voor het maken van Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) veel richtlijnen en suggesties waarmee u Azure Resource Manager-sjablonen maken die betrouwbaar en gebruiksvriendelijk zijn. Meestal gebruikt u een variant van een van de opgegeven benaderingen of voorbeelden en wijzigt u uw sjabloon voor uw behoeften.

## <a name="step-3-deploy-resources-with-powershell"></a>Stap 3: Resources implementeren met PowerShell
Nadat u uw sjablonen en scripts hebt aangepast, voert u de stappen uit die nodig zijn om resources te [implementeren met Resource Manager-sjablonen en Azure PowerShell.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) In het artikel vindt u algemene informatie over het gebruik van Azure PowerShell met Azure Resource Manager-sjablonen om uw resources naar Azure te implementeren.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Veelvoorkomende taken die u uitvoeren in DevTest-labs met PowerShell
Er zijn veel andere veelvoorkomende taken die u automatiseren met PowerShell. In de volgende secties van de documentatie worden de stappen beschreven die nodig zijn om deze taken uit te voeren.

* [Een aangepaste afbeelding maken op basis van een VHD-bestand met PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [VHD-bestand uploaden naar het opslagaccount van het lab met PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Een externe gebruiker toevoegen aan een lab met PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Een aangepaste labrol maken met PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Volgende stappen
* Meer informatie over het maken van een [privé-Git-opslagplaats](devtest-lab-add-artifact-repo.md) waar u uw aangepaste sjablonen of scripts opslaat.
* Bekijk de [Azure Resource Manager-sjablonen in de sjabloongalerie azure Quickstart.](https://github.com/Azure/azure-quickstart-templates)
