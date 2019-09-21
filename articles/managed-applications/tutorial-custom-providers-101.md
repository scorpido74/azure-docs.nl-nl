---
title: Aangepaste acties en resources maken in azure
description: Deze zelf studie gaat over het maken van aangepaste acties en resources in Azure Resource Manager. Daarnaast ziet u hoe aangepaste werk stromen compatibel zijn met Azure Resource Manager sjablonen, Azure CLI, Azure Policy en Azure-activiteiten logboek.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: dc1601e344c371a5f0feaadd272a2c6ff40af031
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172941"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Aangepaste acties en resources maken in azure

Een aangepaste provider is een contract tussen Azure en een eind punt. Met aangepaste providers kunt u werk stromen in azure wijzigen door nieuwe Api's toe te voegen aan Azure Resource Manager. Met deze aangepaste Api's kan Resource Manager gebruikmaken van nieuwe implementatie-en beheer mogelijkheden.

In deze zelf studie wordt een eenvoudig voor beeld van het toevoegen van nieuwe acties en resources aan Azure en hoe u deze kunt integreren.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure Functions instellen voor aangepaste Azure-providers

In deel één van deze zelf studie wordt beschreven hoe u een Azure function-app instelt voor gebruik met aangepaste providers:

- [Azure Functions instellen voor aangepaste Azure-providers](./tutorial-custom-providers-function-setup.md)

Aangepaste providers kunnen werken met elke open bare URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Een REST-eind punt ontwerpen voor aangepaste providers

In deel twee van deze zelf studie wordt beschreven hoe u een REST-eind punt kunt ontwerpen voor aangepaste providers:

- [Een REST-eind punt ontwerpen voor aangepaste providers](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Een aangepaste provider maken en gebruiken

In deel drie van deze zelf studie wordt beschreven hoe u een aangepaste provider maakt en de aangepaste acties en resources gebruikt:

- [Een aangepaste provider maken en gebruiken](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd over aangepaste providers en hoe u er een kunt maken. Zie [zelf studie als u wilt door gaan met de volgende zelf studie: Azure Functions instellen voor aangepaste Azure-providers](./tutorial-custom-providers-function-setup.md).

Als u naar verwijzingen of een Snelstartgids zoekt, vindt u hier enkele nuttige koppelingen:

- [Snelstart: Een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Procedure: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
