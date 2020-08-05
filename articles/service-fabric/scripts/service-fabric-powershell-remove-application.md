---
title: Toepassing verwijderen uit een cluster in PowerShell
description: Azure PowerShell-voorbeeldscript - Een toepassing verwijderen uit een Service Fabric-cluster.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8b4b07288ce2c3570da5482a446b9418c7319011
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086197"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Een toepassing verwijderen uit een Service Fabric-cluster met behulp van PowerShell

Met dit voorbeeldscript wordt een actief exemplaar van de Service Fabric-toepassing verwijderd en wordt de registratie van een type en versie van de toepassing bij het cluster ongedaan gemaakt.  Het verwijderen van het exemplaar van de toepassing betekent dat ook alle actieve service-exemplaren worden verwijderd die aan die toepassing zijn gekoppeld. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Hiermee verwijdert u een actief exemplaar van een Service Fabric-toepassing uit het cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee wordt de registratie van een type en versie van een Service Fabric-toepassing in het cluster ongedaan gemaakt. |

## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure PowerShell](/powershell/azure/service-fabric/overview?view=azureservicefabricps) voor meer informatie over de Service Fabric PowerShell-module.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
