---
title: Zelfstudie – Azure Service Fabric Mesh-resources opschonen
description: Leer hoe u Azure Service Fabric Mesh-resources verwijdert, zodat er geen kosten in rekening worden gebracht voor resources die u niet meer gebruikt.
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b8ce3c795bc9ad212331ce1c1f413fe7fd6da909
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246743"
---
# <a name="tutorial-remove-azure-resources"></a>Zelfstudie: Azure-resources verwijderen

Deze zelfstudie is deel vijf van een reeks zelfstudies en laat zien hoe u de app en de bijbehorende resources verwijderen zodat er geen kosten in rekening worden gebracht.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * De resources opschonen die worden gebruikt door de app zodat er geen kosten in rekening worden gebracht.

In deze zelfstudiereeks leert u het volgende:
> [!div class="checklist"]
> * [Een Service Fabric Mesh-app maken in Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Fouten opsporen in een Service Fabric Mesh-app die wordt uitgevoerd in uw lokale ontwikkelcluster](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Een Service Fabric Mesh-app bijwerken](service-fabric-mesh-tutorial-upgrade.md)
> * Service Fabric Mesh-resources opschonen

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor u met deze zelfstudie begint:

* Als u de to-do-app nog niet hebt geïmplementeerd, volgt u de instructies in [Zelfstudie: een Service Fabric Mesh-webtoepassing implementeren](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="clean-up-resources"></a>Resources opschonen

Dit is het einde van de zelfstudie. Wanneer u de resources die u hebt gemaakt niet meer nodig hebt, verwijdert u deze zodat er geen kosten in rekening gebracht voor resources die u niet meer gebruikt. Dit is vooral belangrijk omdat Mesh een serverloze service is die per seconde wordt gefactureerd. Zie https://aka.ms/sfmeshpricing voor meer informatie over de prijzen voor Mesh.

Een van de voordelen van Azure is dat wanneer u resources maakt die zijn gekoppeld aan een bepaalde resourcegroep, u alleen de resourcegroep hoeft te verwijderen om alle bijbehorende resources te verwijderen. Op die manier hoeft u de resources niet één voor één te verwijderen.

Aangezien u een nieuwe resourcegroep hebt gemaakt voor de to-do-app, kunt u deze resourcegroep veilig verwijderen, en zo dus alle bijbehorende resources verwijderen.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Een andere manier is om de resourcegroep **sfmeshTutorial1RG** te verwijderen [vanuit de portal](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups). 

## <a name="next-steps"></a>Volgende stappen

Nu u een Service Fabric Mesh-toepassing hebt geïmplementeerd in Azure, kunt u het volgende proberen:

* Bekijk de [voorbeeld-app Voting](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) voor een ander voorbeeld van service-naar-service-communicatie.
* Zie [Inleiding tot Service Fabric Resource-model](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het Service Fabric Resource-model.
* Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
* Meer informatie over de [Cloud Shell](../cloud-shell/overview.md)
