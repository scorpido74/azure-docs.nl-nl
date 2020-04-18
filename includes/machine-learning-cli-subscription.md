---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616818"
---
> [!TIP]
> Nadat u zich hebt aanmeldt, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnementsgegevens `isDefault: true` waarmee het momenteel geactiveerde abonnement voor Azure CLI-opdrachten zijn. Dit abonnement moet hetzelfde abonnement zijn dat uw Azure Machine Learning-werkruimte bevat. U de abonnements-id vinden in de [Azure-portal](https://portal.azure.com) door naar de overzichtspagina voor uw werkruimte te gaan. U de SDK ook gebruiken om de abonnements-ID van het werkruimteobject op te halen. Bijvoorbeeld `Workspace.from_config().subscription_id`.
> 
> Als u een ander `az account set -s <subscription name or ID>` abonnement wilt selecteren, gebruikt u de opdracht en geeft u de naam of id op waarnaar u wilt overschakelen. Zie [Meerdere Azure-abonnementen gebruiken](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)voor meer informatie over abonnementsselectie.