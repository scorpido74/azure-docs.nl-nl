---
title: Een Service Fabric-toepassing upgraden in PowerShell
description: Azure PowerShell-voorbeeldscript - Upgrade en controleer een Azure Service Fabric-toepassing met PowerShell.
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
ms.openlocfilehash: 1b04a0c4d2865cf72d9fbccb51f0f083872ea799
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037879"
---
# <a name="upgrade-a-service-fabric-application"></a>Een Service Fabric-toepassing upgraden

Met dit voorbeeldscript wordt een actief exemplaar van de Service Fabric-toepassing geüpgradet naar versie 1.3.0. Met dit script wordt het nieuwe toepassingspakket naar de installatiekopieopslag van het cluster gekopieerd, wordt het toepassingstype geregistreerd en wordt het overbodige toepassingspakket verwijderd.  Het script start een gecontroleerde upgrade en controleert de upgradestatus voortdurend totdat de upgrade is voltooid of terugdraait. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Hiermee worden alle toepassingen in het Service Fabric-cluster of wordt een specifieke toepassing opgehaald.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Hiermee wordt de status van een Service Fabric-toepassingsupgrade opgehaald. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee worden de Service Fabric-toepassingstypen opgehaald die zijn geregistreerd in het Service Fabric-cluster. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee wordt de registratie van een Service Fabric-toepassingstype opgeheven.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee wordt een Service Fabric-toepassingspakket gekopieerd naar de installatiekopieopslag.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Hiermee wordt een Service Fabric-toepassingstype geregistreerd. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Hiermee wordt een Service Fabric-toepassing geüpgraded naar de opgegeven versie van het toepassingstype. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee wordt een Service Fabric-toepassingspakket verwijderd uit de installatiekopieopslag.|


## <a name="next-steps"></a>Volgende stappen

Zie de [documentatie van Azure PowerShell](/powershell/azure/service-fabric/overview?view=azureservicefabricps) voor meer informatie over de Service Fabric PowerShell-module.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
