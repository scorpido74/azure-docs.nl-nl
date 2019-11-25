---
title: Azure dev Spaces delen
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Snelle Kubernetes-ontwikkeling met containers en microservices in Azure
keywords: 'Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S '
ms.openlocfilehash: 2c85625a4f61b701bc1e8b0a4a06f71dc0989ce0
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279982"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces delen

Met Azure Dev Space kunt u uw ontwikkelruimte delen met anderen in uw team. Elke ontwikkelaar kan in zijn eigen ruimte werken zonder angst om die van anderen aan te tasten. Bovendien, door samen te werken in één ruimte kunt u code end-to-end testen zonder mocks te hoeven maken of afhankelijkheden te simuleren. Zie [meer informatie over teamontwikkeling](../team-development-nodejs.md) gids voor meer informatie.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Instellen van een ontwikkelruimte voor meerdere ontwikkelaars

1. Maak een Dev Space in Azure. Kies [.net core en VS code](../get-started-netcore.md), [.net core en Visual Studio](../get-started-netcore-visualstudio.md), of [node. js en VS code](../get-started-nodejs.md). U moet als eigenaar of bijdrager toegang hebben tot de geselecteerde Azure-abonnement.
1. Configureer de Azure Dev Space **resourcegroep** voor [medewerker toegang](/azure/active-directory/role-based-access-control-configure) voor elk teamlid. Met deze opdracht kunt u de resourcegroep van een ontwikkelruimte controleren: `azds list-up`
1. Vraag teamleden om de **ontwikkelruimte te selecteren** om erin te ontwikkelen.
   * **Vanaf de opdrachtregel of in VS Code**: om bestaande Azure Dev Spaces waar u toegang toe hebt te zien: `azds space list`. Om een ontwikkelruimte te selecteren: `azds space select`.
   * **Visual Studio IDE**: Open een project in Visual Studio, selecteer **Azure Dev Spaces** in de start-instellingen vervolgkeuzelijst. In het dialoogvenster dat wordt geopend selecteert u een bestaand cluster.

     ![Visual Studio start-instellingen vervolgkeuzelijst](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Volgende stappen

Zie [informatie over team ontwikkeling](../team-development-nodejs.md) voor meer informatie.
