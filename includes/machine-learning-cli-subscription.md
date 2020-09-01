---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81616818"
---
> [!TIP]
> Nadat u zich hebt aangemeld, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnementsgegevens met `isDefault: true` zijn het abonnement dat momenteel is geactiveerd voor Azure CLI-opdrachten. Dit abonnement moet gelijk zijn aan het abonnement dat uw Azure Machine Learning-werkruimte bevat. U vindt het abonnements-id via de [Azure-portal](https://portal.azure.com), door de pagina Overzicht voor uw werkruimte te bezoeken. U kunt ook de SDK gebruiken om het abonnements-id van het werkruimteobject op te halen. Bijvoorbeeld `Workspace.from_config().subscription_id`.
> 
> Als u een ander abonnement wilt selecteren, gebruikt u de opdracht `az account set -s <subscription name or ID>` en geeft u de naam van het abonnement of het id op om over te schakelen. Zie [Meerdere Azure-abonnementen gebruiken](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) voor meer informatie over het selecteren van abonnementen.