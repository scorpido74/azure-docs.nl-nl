---
title: Azure Dev-ruimten delen
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van Azure Dev Spaces om een dev-ruimte in Azure Kubernetes Service te delen met anderen in uw team
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474404"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces delen

Met Azure Dev Spaces u uw dev-ruimte delen met anderen in uw team. Elke ontwikkelaar kan werken in hun eigen ruimte zonder angst voor het breken van anderen. Als u in één ruimte samenwerkt, u code ook end-to-end testen zonder mocks te hoeven maken of afhankelijkheden te simuleren. Zie de gids Voor meer informatie [over teamontwikkeling.](../team-development-nodejs.md)

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Een ontwikkelruimte instellen voor meerdere ontwikkelaars

1. Maak een Dev-ruimte in Azure. Kies [.NET Core en VS Code,](../get-started-netcore.md) [.NET Core en Visual Studio](../get-started-netcore-visualstudio.md), of [Node.js en VS Code](../get-started-nodejs.md). U moet eigenaar of inzender toegang hebben tot het geselecteerde Azure-abonnement.
1. Controleer of elk teamlid over de juiste machtigingen beschikt [om toegang te krijgen tot de Azure Dev Spaces-controller.](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis) U bijvoorbeeld de **resourcegroep** van Azure Dev Space configureren om [inzendertoegang te verlenen](/azure/active-directory/role-based-access-control-configure) voor elk teamlid. U de resourcegroep van een dev-ruimte controleren door deze opdracht uit te voeren:`azds show-context`
1. Vraag teamleden om **de dev-ruimte** te selecteren om zich erin te ontwikkelen.
   * **Opdrachtregel of VS-code:** om bestaande Azure Dev-spaties te bekijken waartoe u toegang hebt: `azds space list`. Een dev-ruimte `azds space select`selecteren: .
   * **Visual Studio IDE**: Open een project in Visual Studio, selecteer **Azure Dev Spaces** in de vervolgkeuzelijst voor de lanceringsinstellingen. Selecteer in het dialoogvenster dat wordt geopend een bestaand cluster.

     ![Vervolgkeuzelijst voor de lancering van Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Meer informatie over teamontwikkeling](../team-development-nodejs.md) voor meer informatie.
