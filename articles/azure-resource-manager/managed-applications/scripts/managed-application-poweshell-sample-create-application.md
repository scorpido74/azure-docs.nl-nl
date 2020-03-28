---
title: Voorbeeld van Azure PowerShell-script - Een beheerde toepassing implementeren
description: Biedt een voorbeeld van Azure PowerShell-voorbeeld dat een beheerde toepassingsdefinitie implementeert in het abonnement.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: e076128a5be453028635b1657bf3f90980863148
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650168"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>Een beheerde toepassing implementeren voor een servicecatalogus met Azure PowerShell

Met dit script implementeert u de definitie van een beheerde toepassing uit de servicecatalogus.


[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om de beheerde toepassing te implementeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplication) | Hiermee maakt u een beheerde toepassing. Geef de definitie-id en de parameters op voor de sjabloon. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.
