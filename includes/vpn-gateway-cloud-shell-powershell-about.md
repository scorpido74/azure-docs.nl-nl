---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045093"
---
In dit artikel worden Power shell-cmdlets gebruikt. Als u de cmdlets wilt uitvoeren, kunt u Azure Cloud Shell, een interactieve shell-omgeving die wordt gehost in azure, gebruiken via de browser. Azure Cloud Shell wordt geleverd met de Azure PowerShell-cmdlets die vooraf zijn geïnstalleerd.

Als u in dit artikel een code wilt uitvoeren op Azure Cloud Shell, opent u een Cloud Shell-sessie, gebruikt u de knop **kopiëren** in een code blok om de code te kopiëren en plakt u deze in de Cloud shell-sessie met __CTRL + SHIFT + v__ in Windows en Linux, of __Cmd + Shift + v__ op macOS. Geplakte tekst wordt niet automatisch uitgevoerd, dus druk op **Enter** om code uit te voeren.

U kunt Azure Cloud Shell starten met:

|  |   |
|-----------------------------------------------|---|
| Selecteer **Nu proberen** in de rechterbovenhoek van een codeblok. Hiermee __wordt tekst niet__ automatisch gekopieerd naar Cloud shell. | ![Voor beeld van uitproberen voor Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Open [shell.Azure.com](https://shell.azure.com) in uw browser. | [knop ![starten Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Klik op de knop **Cloud Shell** in het menu in de hoek rechtsboven in de [Azure Portal](https://portal.azure.com). | ![Knop Cloud Shell in de Azure Portal](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Power shell lokaal uitvoeren**

U kunt ook de Azure PowerShell-cmdlets lokaal op uw computer installeren en uitvoeren. Power shell-cmdlets worden regel matig bijgewerkt. Als u niet de nieuwste versie uitvoert, kunnen de in de instructies opgegeven waarden mislukken. Gebruik de `Get-Module -ListAvailable Az`-cmdlet om te zoeken naar de versies van Azure PowerShell die op uw computer zijn geïnstalleerd. Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps)om te installeren of bij te werken.

Als u Power shell lokaal uitvoert, moet u ' Connect-AzAccount ' uitvoeren om uw verbinding met Azure te maken.