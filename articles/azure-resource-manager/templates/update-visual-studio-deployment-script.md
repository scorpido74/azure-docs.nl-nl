---
title: Het sjabloon implementatie script van Visual Studio bijwerken om AZ Power shell te gebruiken
description: Het Visual Studio-sjabloon implementatie script bijwerken vanuit AzureRM naar AZ Power shell
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473512"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Het Visual Studio-sjabloon implementatie script voor het gebruik van AZ Power shell bijwerken

Visual Studio 16,4 ondersteunt het gebruik van AZ Power shell in het sjabloon implementatie script. Visual Studio installeert AZ Power shell niet of werkt het implementatie script in het project van de resource groep automatisch bij. Als u de nieuwere AZ Power shell wilt gebruiken, moet u de volgende vier dingen doen:
1. AzureRM Power shell verwijderen
1. Installeren AZ Power shell
1. Bijwerken naar Visual Studio 16,4
1. Werk het implementatie script bij in uw project.

## <a name="uninstall-azurerm-powershell"></a>AzureRM Power shell verwijderen
Volg deze [instructies](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) voor het verwijderen van AzureRM Power shell.

## <a name="install-az-powershell"></a>Installeren AZ Power shell
Volg deze [instructies](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) om AZ Power shell te installeren.

## <a name="update-visual-studio-to-164"></a>Update Visual Studio op 16,4
Werk bij naar Visual Studio 16,4 wanneer dit beschikbaar is. Zorg er tijdens de upgrade voor dat het onderdeel van de Azure Power shell niet is ingeschakeld. Sinds u AZ Power shell hebt geïnstalleerd via de galerie, wilt u niet dat de AzureRM-versie van Power shell wordt geïnstalleerd met Visual Studio.

Als u al een upgrade hebt uitgevoerd naar 16,4 en het onderdeel van Azure Power shell is gecontroleerd, kunt u het verwijderen door het Visual Studio-installatie programma uit te voeren en het onderdeel van Azure Power shell in de Azure-werk belasting uit te scha passen of op de pagina afzonderlijke onderdelen.

## <a name="update-the-deployment-script-in-your-project"></a>Het implementatie script in uw project bijwerken
Vervang alle instanties van de teken reeks ' AzureRm ' door AZ ' in uw implementatie script. U kunt [in deze informatie over](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) revisies verwijzen om de wijzigingen te bekijken. Raadpleeg deze [documentatie](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) voor meer informatie over het bijwerken van scripts naar AZ Power shell.


