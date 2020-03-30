---
title: Toepassing verwijderen uit een cluster in Powershell
description: 'Azure PowerShell Script-voorbeeld : verwijder een toepassing uit een cluster van servicefabric.'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610281"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Een toepassing verwijderen uit een cluster van Servicefabric met Powershell

Met dit voorbeeldscript wordt een actief uitvoerende servicefabric-toepassingsinstantie verwijderd en wordt een toepassingstype en -versie uit het cluster verwijderd.  Het verwijderen van het exemplaar van de toepassing betekent dat ook alle actieve service-exemplaren worden verwijderd die aan die toepassing zijn gekoppeld. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Remove-serviceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Hiermee verwijdert u een actief exemplaar van de Service Fabric-toepassingsinstantie uit het cluster.  |
| [Register-ServiceFabricApplicationType uitschrijven](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | De registratie van een servicefabric-toepassingstype en -versie uit het cluster wordt losgekoppeld. |

## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/service-fabric/?view=azureservicefabricps)voor meer informatie over de PowerShell-module servicefabric.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
