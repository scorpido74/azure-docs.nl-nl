---
title: Aangepaste acties en resources maken
description: In deze zelfstudie wordt uitgelegd hoe u aangepaste acties en resources maakt in Azure Resource Manager. Het toont ook hoe aangepaste werkstromen samenwerken met Azure Resource Manager Templates, Azure CLI, Azure Policy en Azure Activity Log.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649895"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Aangepaste acties en resources maken in Azure

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers u werkstromen in Azure wijzigen door nieuwe API's toe te voegen aan Azure Resource Manager. Met deze aangepaste API's kan Resource Manager nieuwe implementatie- en beheermogelijkheden gebruiken.

Deze zelfstudie gaat door een eenvoudig voorbeeld van hoe u nieuwe acties en resources toevoegt aan Azure en hoe u deze integreren.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure-functies instellen voor Azure Custom Providers

Deel één van deze zelfstudie beschrijft hoe u een Azure-functie-app instelt om te werken met aangepaste providers:

- [Azure-functies instellen voor Azure Custom Providers](./tutorial-custom-providers-function-setup.md)

Aangepaste providers kunnen werken met elke openbare URL.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Een RESTful-eindpunt voor aangepaste providers maken

Deel twee van deze zelfstudie beschrijft hoe u een RESTful-eindpunt voor aangepaste providers maakt:

- [Een RESTful-eindpunt voor aangepaste providers maken](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Een aangepaste provider maken en gebruiken

Deel drie van deze zelfstudie beschrijft hoe u een aangepaste provider maakt en de aangepaste acties en resources gebruikt:

- [Een aangepaste provider maken en gebruiken](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie leerde u over aangepaste providers en hoe u er een bouwen. Zie [Zelfstudie: Azure-functies instellen voor Azure Custom Providers](./tutorial-custom-providers-function-setup.md)om door te gaan naar de volgende zelfstudie.

Als u op zoek bent naar referenties of een quickstart, zijn hier enkele nuttige links:

- [Snelstart: een azure-leverancier van aangepaste resources maken en aangepaste resources implementeren](./create-custom-provider.md)
- [How to: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [How to: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
