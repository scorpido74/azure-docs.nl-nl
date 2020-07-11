---
title: Azure dev Spaces delen
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Meer informatie over het gebruik van Azure dev Spaces om een dev-ruimte in azure Kubernetes-service te delen met anderen in uw team
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s '
ms.openlocfilehash: 3cef7cd6a5113401cb1df3dd6c76e4dea3a7524d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229292"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces delen

Met Azure dev Spaces kunt u uw ontwikkel ruimte delen met anderen in uw team. Elke ontwikkelaar kan in hun eigen ruimte werken zonder dat anderen dat hoeven te verbreken. Daarnaast kunt u in één Space samen werken om de code end-to-end te testen zonder dat u de modellen hoeft te maken of afhankelijkheden moet simuleren. Raadpleeg de hand leiding [over team Development](../team-development-nodejs.md) voor meer informatie.

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Een ontwikkel ruimte voor meerdere ontwikkel aars instellen

1. Maak een ruimte voor ontwikkel aars in Azure. Kies [.net core en VS code](../get-started-netcore.md), [.net core en Visual Studio](../get-started-netcore-visualstudio.md), of [Node.js en VS code](../get-started-nodejs.md). U moet eigenaar of Inzender toegang hebben tot het geselecteerde Azure-abonnement.
1. Zorg ervoor dat elk teamlid over de [juiste machtigingen beschikt om toegang te krijgen tot de Azure dev Spaces-controller](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis). U kunt bijvoorbeeld de **resource groep** Azure dev Space configureren om de toegang van [mede](../../role-based-access-control/role-assignments-portal.md) werkers voor elk teamlid te verlenen. U kunt de resource groep van een ontwikkel ruimte controleren door deze opdracht uit te voeren:`azds show-context`
1. Vraag team leden om **de ontwikkelings ruimte te selecteren** om deze te ontwikkelen.
   * **Opdracht regel of VS-code**: als u bestaande Azure dev Spaces wilt bekijken, hebt u toegang tot: `azds space list` . Een ontwikkelings ruimte selecteren: `azds space select` .
   * **Visual Studio IDE**: Open een project in Visual Studio, selecteer **Azure dev Spaces** in de vervolg keuzelijst start instellingen. Selecteer een bestaand cluster in het dialoog venster dat wordt geopend.

     ![Vervolg keuzelijst start instellingen Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Volgende stappen

Zie [informatie over team ontwikkeling](../team-development-nodejs.md) voor meer informatie.
