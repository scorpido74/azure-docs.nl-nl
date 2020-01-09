---
title: Aangepaste acties en resources maken
description: Deze zelf studie gaat over het maken van aangepaste acties en resources in Azure Resource Manager. Daarnaast ziet u hoe aangepaste werk stromen compatibel zijn met Azure Resource Manager sjablonen, Azure CLI, Azure Policy en Azure-activiteiten logboek.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649895"
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

In deze zelf studie hebt u geleerd over aangepaste providers en hoe u er een kunt maken. Zie [zelf studie: Azure functions instellen voor aangepaste Azure-providers](./tutorial-custom-providers-function-setup.md)om door te gaan met de volgende zelf studie.

Als u naar verwijzingen of een Snelstartgids zoekt, vindt u hier enkele nuttige koppelingen:

- [Snelstartgids: een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Procedure: aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
