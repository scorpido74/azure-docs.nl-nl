---
title: Toepassing implementeren op een cluster in Powershell
description: Azure PowerShell Script-voorbeeld - Een toepassing implementeren in een cluster van servicefabric.
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
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610298"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Een toepassing implementeren naar een Service Fabric-cluster

Met dit voorbeeldscript wordt een toepassingspakket naar een clusterafbeeldingsarchief gekopieerd, wordt het toepassingstype in het cluster geregistreerd, wordt het onnodige toepassingspakket verwijderd en wordt een toepassingsinstantie gemaakt van het toepassingstype.  Als standaardservices zijn gedefinieerd in het toepassingsmanifest van het doeltoepassingstype, worden deze services op dit moment gemaakt. Pas de parameters zo nodig aan. 

Installeer indien nodig de Service Fabric PowerShell-module met de [Service Fabric-SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het scriptvoorbeeld is uitgevoerd, kan het script in [Een toepassing verwijderen](service-fabric-powershell-remove-application.md) worden gebruikt om de toepassingsinstantie te verwijderen, het toepassingstype uit te schrijven en het toepassingspakket uit het afbeeldingsarchief te verwijderen.

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
|[Connect-serviceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Hiermee maakt u een verbinding met een cluster van servicefabric. |
|[Copy-serviceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee kopieert u een toepassingspakket naar het clusterimagearchief.  |
|[Register-serviceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Registreert een toepassingstype en -versie op het cluster. |
|[Nieuwe-serviceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Hiermee maakt u een toepassing op basis van een geregistreerd toepassingstype. |
| [Remove-serviceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Hiermee verwijdert u een servicefabric-toepassingspakket uit het afbeeldingsarchief.|

## <a name="next-steps"></a>Volgende stappen

Zie [Azure PowerShell-documentatie](/powershell/azure/service-fabric/?view=azureservicefabricps)voor meer informatie over de PowerShell-module servicefabric.

Meer PowerShell-voorbeelden voor Azure Service Fabric vindt u in de [voorbeelden van Azure PowerShell](../service-fabric-powershell-samples.md).
