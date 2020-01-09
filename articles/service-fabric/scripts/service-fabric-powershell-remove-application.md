---
title: Toepassing verwijderen uit een cluster in Power shell
description: Azure PowerShell-voorbeeld script-een toepassing verwijderen uit een Service Fabric cluster.
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
ms.openlocfilehash: 9ac4c23468c74b5a2c6874de2cb6b8d0e6b9e7dd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610281"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Een toepassing verwijderen uit een Service Fabric-cluster met behulp van Power shell

Met dit voorbeeld script wordt een actief Service Fabric toepassings exemplaar verwijderd en wordt de registratie van een toepassings type en-versie uit het cluster ongedaan gemaakt.  Het verwijderen van het exemplaar van de toepassing betekent dat ook alle actieve service-exemplaren worden verwijderd die aan die toepassing zijn gekoppeld. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Hiermee verwijdert u een actief Service Fabric toepassings exemplaar uit het cluster.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee wordt de registratie van een Service Fabric toepassings type en-versie uit het cluster ongedaan gemaakt. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/service-fabric/?view=azureservicefabricps)voor meer informatie over de service Fabric Power shell-module.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
