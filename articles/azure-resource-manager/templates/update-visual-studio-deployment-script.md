---
title: Het sjabloonimplementatiescript van Visual Studio bijwerken om Az PowerShell te gebruiken
description: Het implementatiescript voor Visual Studio-sjablonen bijwerken van AzureRM naar Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963866"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Implementatiescript voor Visual Studio-sjablonen bijwerken om de Az PowerShell-module te gebruiken

Visual Studio 16.4 ondersteunt het gebruik van de Az PowerShell-module in het script voor het implementeren van sjablonen. Visual Studio installeert die module echter niet automatisch. Als u de Az-module wilt gebruiken, moet u vier stappen uitvoeren:

1. [AzureRM-module verwijderen](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Az-module installeren](/powershell/azure/install-az-ps)
1. Visual Studio bijwerken naar 16.4
1. Werk het implementatiescript in uw project bij.

## <a name="update-visual-studio-to-164"></a>Visual Studio bijwerken naar 16.4

Werk uw installatie van Visual Studio bij naar versie 16.4 of hoger. Controleer tijdens de upgrade of het Azure PowerShell-onderdeel niet is gecontroleerd. Aangezien u de Az-module via de galerie hebt geïnstalleerd, wilt u de AzureRM-module niet opnieuw installeren.

Als u al een upgrade hebt uitgevoerd naar 16.4 en het Azure PowerShell-onderdeel is ingeschakeld, u deze verwijderen door het Visual Studio Installer uit te voeren. Selecteer de component Azure PowerShell niet in de Azure-werkbelasting of op de pagina afzonderlijke onderdelen.

## <a name="update-the-deployment-script-in-your-project"></a>Het implementatiescript in uw project bijwerken

Vervang alle exemplaren van de tekenreeks 'AzureRm' door 'Az' in uw implementatiescript. U verwijzen naar revisies in deze [kern](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) om de wijzigingen te zien. Zie [Azure PowerShell migreren van AzureRM naar Az voor](/powershell/azure/migrate-from-azurerm-to-az)meer informatie over het upgraden van scripts naar de Az-module.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure-resourcegroepprojecten maken en implementeren via Visual Studio](create-visual-studio-deployment-project.md)voor meer informatie over het gebruik van het Visual Studio-project.
